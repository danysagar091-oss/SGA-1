# Q2: Secure Project Workspace Setup - Explanation

## Command 1: `mkdir -p secure_workspace/{docs,src,logs}`
**Observation:** This command creates a hierarchical directory structure using brace expansion. The `-p` flag ensures parent directories are created if they don't exist. We created a secure_workspace parent directory with three subdirectories: docs, src, and logs. This organization separates different types of project files into logical sections.

## Command 2: `touch` command for file creation
**Observation:** The touch command creates empty files in their respective directories. We created plan.txt in docs, app.txt in src, and activity.log in logs. These files serve as placeholders for project documentation, application code, and activity logs respectively.

## Command 3: Initial Permission Listing
**Observation:** Before applying security restrictions, all directories showed 777 permissions (drwxrwxrwx+). This means everyone (owner, group, and others) has full read-write-execute access, which is a significant security risk. The "+" symbol indicates ACL (Access Control List) extended permissions are present.

## Command 4 & 5: `chmod 750` on directories
**Observation:** We restricted directory permissions to 750 (rwxr-x---), which means only the owner can write, the group can read and execute, and others have no access. This protects the project data from unauthorized access while allowing group members to browse the directory structure.

## Command 6: File Permission Issue
**Observation:** The chmod command for files partially failed due to a typo (activity instead of activity.log). This demonstrates the importance of careful command entry. The activity.log file retained its default 666 permissions (rw-rw-rw-) while plan.txt and app.txt were set to 640 (rw-r-----).

## Command 7: Permissions After Changes
**Observation:** The directory permissions changed from 777 to 750, significantly reducing security exposure. The directories now follow the principle of least privilege, where only the owner and group members have access, and others are completely restricted.

## Command 8: File Permissions Listing
**Observation:** Different files have different permissions: plan.txt and app.txt are 640 (read-only for group), while activity.log is 666. This inconsistency shows that activity.log is a security vulnerability requiring additional attention and should be corrected to 640 or more restrictive permissions.

## Command 9: Umask Value
**Observation:** The umask is 0022, which means new files are created with 644 permissions and directories with 755 permissions by default. Understanding umask is crucial for setting appropriate default permissions on new files created in this workspace going forward.

## Security Summary
- **Directory Protection:** 750 permissions ensure only authorized users (owner and group) can access project files
- **File Confidentiality:** 640 permissions on critical files prevent unauthorized reading
- **Principle of Least Privilege:** Each resource has the minimum necessary permissions
- **Recommendation:** Change activity.log to 640 permissions to maintain consistent security across all project files
