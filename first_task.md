# Dynamic Backup and Synchronization Tool Assignment

## Overview
Create a robust bash script named `backup_sync.sh` that serves as an automated backup and synchronization tool. The script must handle incremental backups, encryption, remote synchronization, restoration, and logging with advanced error handling and security considerations.

## Task Details
Develop a bash script that performs the following functions:

1. **Configuration Management**:
   - Read settings from a configuration file (`/etc/backup_sync.conf`) specifying source directories, backup destination (local or remote), encryption settings, retention policy, and sync schedule.
   - Support command-line arguments to override defaults (`-c` for config file, `-s` for source directories, `-d` for destination, `-e` for encryption toggle).

2. **Backup Operations**:
   - Perform incremental backups of specified source directories to a local or remote destination using `rsync` with compression and bandwidth limiting.
   - Support exclusion patterns (e.g., exclude temporary files or specific extensions) defined in the config file.
   - Create timestamped backup archives (e.g., `.tar.gz`) for each backup run.

3. **Encryption**:
   - Optionally encrypt backup archives using `gpg` with a symmetric or asymmetric key (configurable in the config file).
   - Store encryption keys securely and prompt for passphrase if needed during backup or restore.

4. **Retention Policy**:
   - Implement a retention policy to keep a configurable number of backups (e.g., last 7 daily backups, 4 weekly backups).
   - Automatically delete older backups while preserving the most recent ones.

5. **Remote Synchronization**:
   - Support syncing backups to a remote server via SSH using `rsync`.
   - Handle SSH key-based authentication and validate remote connectivity before starting the sync.

6. **Restore Functionality**:
   - Provide a restore mode (`--restore`) that lists available backups and allows the user to select one for restoration.
   - Decrypt (if encrypted) and extract the selected backup to a specified directory.

7. **Logging and Notifications**:
   - Log all operations (backup, sync, restore, errors) to a file (`/var/log/backup_sync.log`) with timestamps.
   - Implement log rotation when the log file exceeds a configurable size (default: 5MB).
   - Send email notifications on success or failure using the `mail` command.

8. **Concurrency Control**:
   - Prevent multiple instances of the script from running simultaneously using a lock file (`/var/run/backup_sync.lock`).
   - Handle stale lock files gracefully (e.g., check if the process is still running).

9. **Error Handling and Recovery**:
   - Validate all inputs (e.g., source/destination paths, SSH connectivity, GPG keys).
   - Implement retry logic for failed `rsync` or `gpg` operations (e.g., retry up to 3 times with exponential backoff).
   - Use appropriate exit codes for different failure scenarios (e.g., config errors, file access issues, network failures).

10. **Security Considerations**:
    - Ensure secure handling of encryption keys and SSH credentials.
    - Set appropriate permissions for backup files, logs, and lock files (e.g., 600 for sensitive files).
    - Sanitize inputs to prevent command injection when parsing config or executing commands.

## Requirements
- The script must be written entirely in bash, using standard Linux tools (`rsync`, `tar`, `gpg`, `ssh`, `mail`, etc.).
- Organize the script into modular functions (e.g., `backup`, `sync_remote`, `restore`, `rotate_logs`).
- Support both interactive (restore mode) and non-interactive (scheduled backups) operation.
- Handle edge cases, such as missing dependencies, invalid paths, or network interruptions.
- Include detailed comments explaining the script’s logic and usage instructions.

## Expected Challenges
- **Complexity**: Managing incremental backups, encryption, and remote sync requires careful handling of `rsync` and `gpg` options.
- **Concurrency**: Implementing robust lock file management to prevent race conditions is non-trivial.
- **Error Handling**: Retry logic and recovery from network or file system failures demand advanced scripting techniques.
- **Security**: Securely managing encryption keys and SSH credentials while avoiding vulnerabilities is critical.
- **Usability**: Balancing automation (scheduled backups) with interactivity (restore mode) adds complexity.

## Evaluation Criteria
- **Functionality**: Does the script correctly handle backups, encryption, remote sync, and restoration?
- **Robustness**: Are errors, edge cases, and failures handled gracefully with retries and appropriate logging?
- **Security**: Are sensitive data (keys, credentials) handled securely, and are permissions set correctly?
- **Code Quality**: Is the script modular, well-documented, and maintainable?
- **Efficiency**: Does the script optimize resource usage (e.g., bandwidth limiting, incremental backups)?

## Sample Configuration File
Create a configuration file at `/etc/backup_sync.conf` with the following content:
```plaintext
SOURCE_DIRS="/home/user/docs /var/www"
DEST_DIR="/backups"
REMOTE_DEST="user@backup-server:/backups"
EXCLUDE_PATTERNS="*.tmp *.log"
ENCRYPTION_ENABLED=true
GPG_KEY_ID="backup_key"
RETENTION_DAILY=7
RETENTION_WEEKLY=4
LOG_FILE="/var/log/backup_sync.log"
MAX_LOG_SIZE=5242880
EMAIL_NOTIFICATION="admin@example.com"
RSYNC_BW_LIMIT=1000
```

## Usage Examples
```bash
# Run backup with default config
./backup_sync.sh

# Override config file and source directory
./backup_sync.sh -c /etc/backup_sync.conf -s /data -d /mnt/backups

# Restore mode
./backup_sync.sh --restore
```
