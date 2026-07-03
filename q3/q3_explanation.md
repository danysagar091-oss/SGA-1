# Q3: File System and Link Analysis - Explanation

## Command 1-2: File Creation
**Observation:** We created a simple text file containing "Linux link test file" to serve as the original file for our link analysis. This establishes the base file with content that we can verify through different link types.

## Command 3: Hard Link Creation
**Observation:** The `ln` command without flags creates a hard link. A hard link is essentially another directory entry pointing to the exact same inode as the original file. Both files share the same inode number (1310805) and the link count increases from 1 to 2, indicating two references to the same data block on disk.

## Command 4: Symbolic Link Creation
**Observation:** The `ln -s` command creates a symbolic (soft) link, which is fundamentally different from a hard link. A symbolic link is a special file type that stores a path string rather than pointing directly to an inode. The symlink has its own inode (1310806) and file size of 12 bytes, which represents the length of the path "original.txt".

## Command 5: Link Comparison with `ls -li`
**Observation:** The inode listing clearly shows the distinction between link types:
- Hard link and original file: Same inode (1310805), link count of 2
- Symbolic link: Different inode (1310806), link count of 1, permission 777 (lrwxrwxrwx)
This demonstrates that hard links are indistinguishable from regular files after creation, while symbolic links maintain their own identity in the filesystem.

## Command 6: Metadata Analysis with `stat`
**Observation:** The stat command provides detailed inode information confirming that hard link and original file share identical metadata (size, timestamps, permissions). The symbolic link shows size 12 (the path string) and different timestamps, confirming it's a separate file entity storing a reference.

## Command 7-9: Content Access Test
**Observation:** All three files can read and display the same content before deletion. This works because the hard link accesses the same inode directly, while the symbolic link follows the path reference to reach the original file's inode. Both mechanisms successfully retrieve the file content.

## Command 10-11: Original File Deletion
**Observation:** After deleting original.txt, the link count for inode 1310805 decreases to 1, but the inode persists on disk because the hard link maintains another reference. The symbolic link now shows as broken (arrow still points to non-existent original.txt), which is the expected behavior.

## Command 12: Hard Link Access After Deletion
**Observation:** The hard link can still read and display file content because it directly references the inode (1310805) which still exists on disk. The data blocks are not freed until all hard links are deleted, ensuring data integrity and accessibility through any remaining hard link.

## Command 13: Symbolic Link Access After Deletion
**Observation:** The symbolic link becomes broken and returns "No such file or directory" error. Since the symbolic link stores a filename path rather than an inode reference, when the filename no longer exists, the path resolution fails completely. This is a key limitation of symbolic links.

## Summary and Conclusions
- **Hard Links:** Direct inode references, survive target deletion, identical metadata, same link count
- **Symbolic Links:** Path references, break when target is deleted, separate inode, different metadata
- **Use Cases:** Hard links for data protection and backup; symbolic links for creating shortcuts and managing complex directory structures
- **Data Persistence:** Hard links prevent accidental data loss through link count management; symbolic links are more prone to becoming broken
- **Filesystem Design:** Understanding these link types is crucial for effective file management and disaster recovery strategies
