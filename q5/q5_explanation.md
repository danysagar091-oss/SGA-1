# Q5: Storage Health Assessment - Explanation

## Command 1: Block Device Listing (`lsblk`)
**Observation:** The `lsblk` command provides a hierarchical view of all block devices in the system. We identified:
- **Loop Devices:** 6 virtual block devices used by Docker containers and virtual machines (loop0-5)
- **Physical Disks:** sda (45GB), sdb (30GB), sdc (512GB), and sr0 (CD-ROM)
- **Partitions:** Displayed as sub-entries under parent disks (sda1, sdb1, sdc1)

This command is essential for understanding the storage topology before making deployment decisions.

## Command 2: Filesystem Mount Information (`mount`)
**Observation:** The `mount` command reveals which filesystems are currently active and mounted at specific locations:
- **Root filesystem (/):** Uses overlay type from Docker
- **Proc, sysfs, tmpfs:** Virtual filesystems providing kernel interface and temporary storage
- **Mount flags:** Show read/write status (rw), noexec (no executable permissions), relabel options
- **Mount points:** Indicate where each filesystem is accessible

Understanding mount configuration is critical for troubleshooting file access and resource allocation issues.

## Command 3: Disk Usage Analysis (`df -h`)
**Observation:** The `df -h` command displays space utilization in human-readable format:

**Critical Alert - High Disk Usage:**
- `/dev/root (82% full)` and `/dev/sdb1 (82% full)` are dangerously close to capacity
- Best practice threshold is 80% - these filesystems have exceeded safe limits
- Remaining space: Only 5.3GB available on each (risk of application failures)

**Healthy Filesystems:**
- Root overlay (33%), /tmp (7%), and /workspaces (33%) have adequate free space
- tmpfs devices show 0% usage as they are in-memory temporary storage

**Implications:**
- Risk of "No space left on device" errors
- Performance degradation may occur
- Database writes and log rotations could fail

## Command 4: Inode Usage Analysis (`df -i`)
**Observation:** The `df -i` command displays inode utilization rather than block storage:

**Inode Status - All Healthy:**
- Highest usage: 10% on overlay, loop4, and sdb1
- Lowest usage: 1% on /tmp and tmpfs devices
- All filesystems have abundant free inodes (>90% available)

**Important Note:**
- Even if disk space is full, if inodes are available, file creation might still be possible (partially)
- Conversely, inode exhaustion can prevent file creation despite available disk space
- Current system has no inode-related concerns

## Storage Assessment Summary

### Current Status:
| Filesystem | Size | Used | Available | Status |
|-----------|------|------|-----------|--------|
| /dev/root | 29GB | 24GB | 5.3GB | ⚠️ CRITICAL - 82% full |
| /dev/sdb1 | 29.9GB | 24GB | 5.3GB | ⚠️ CRITICAL - 82% full |
| Root (overlay) | 32GB | 9.6GB | 21GB | ✓ HEALTHY - 33% full |
| /tmp | 44GB | 2.7GB | 40GB | ✓ HEALTHY - 7% full |
| /workspaces | 32GB | 9.6GB | 21GB | ✓ HEALTHY - 33% full |
| Inodes | All | 10% max | >90% free | ✓ HEALTHY |

### Recommendations for Improving Storage Utilization:

1. **Immediate Actions (Critical):**
   - Analyze and clean up /dev/root and /dev/sdb1 filesystems
   - Identify large files or unnecessary data consuming space
   - Implement log rotation to prevent log file accumulation
   - Archive or remove old Docker images and unused containers

2. **Short-term Solutions:**
   - Monitor disk usage with alerting thresholds (trigger at 75%)
   - Implement automated cleanup scripts for temporary files
   - Use disk quota systems to limit user/application storage

3. **Long-term Planning:**
   - Expand /dev/root partition (currently constrained at 82%)
   - Consider utilizing the unpartitioned sdc disk (512GB available)
   - Implement tiered storage strategy (hot/cold data separation)
   - Set up centralized logging with log aggregation tools

4. **Best Practices:**
   - Regular monitoring: Schedule daily `df -h` checks
   - Capacity planning: Track growth trends and predict full dates
   - Proactive maintenance: Clean up before reaching 80% threshold
   - Documentation: Maintain storage allocation and usage records

### Deployment Readiness:
The system is **NOT READY** for new application deployment until /dev/root and /dev/sdb1 are brought below 80% utilization. The 512GB sdc disk should be configured and utilized to alleviate the critical space shortage.
