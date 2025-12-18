# I Deleted a 5GB log file from a production server that was running out of space. 
Akhilesh MishraView

Early in my DevOps career, I deleted a 5GB log file from a production server that was running out of space. 

I ran df -h expecting to see the disk usage drop. It didn’t. 
Still showed 100% full.

No errors, no warnings. Just the same disk usage as before I deleted anything.

That’s when I learned that deleting a file doesn’t always free up space immediately.

In Linux, what we think of as a “file” is actually two separate things: the filename (which is just a pointer) and the inode (which contains the actual data and metadata). When you delete a filename, you’re only removing the pointer. The inode and its data remain on disk as long as any process still has the file open.

In my case, the web server was still writing to that log file. Even though I had deleted the filename, the server process kept its file handle open. The inode stayed alive, invisible to normal file listings but still consuming disk space.

The space was only freed when I restarted the web server, which closed all its file handles.

This is why you need different commands to see the full picture:

# Check filesystem usage
- df -h

# Check actual directory sizes 
- du -sh /var/log/*

# Find deleted files still open by processes
- lsof +L1

The du command shows you what’s actually using space in directories, while df shows filesystem-level usage. 

When they don’t match, you often have deleted files still held open by running processes.

This is also why proper log rotation doesn’t just delete files. Tools like logrotate rename files and send signals to processes so they can close and reopen their file handles cleanly.

Three key takeaways:

1. Filenames are just pointers to inodes
1. Deletion only happens when no processes reference the inode
1. Always check both df and du when troubleshooting disk space

It’s a small detail, but understanding it can save you from confusing production incidents.

