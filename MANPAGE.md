## FX-FLAGBASE(1) Manpage

### NAME
`FX-FLAGBASE` - A file-based utility for managing key-value pairs in custom environment profiles.

### SYNOPSIS
`kvfb [options] [command] <arguments...>`

### DESCRIPTION
FX-FLAGBASE is a utility that leverages the filesystem for the storage and management of key-value pairs, facilitating the dynamic editing of environment profiles. It separates the context of variables from their execution environment and offers mechanisms for handling both session-specific and global (locked) keyfiles.

### KEY CONCEPTS
- **Keyfiles and KV Filebase Format (KVFB):** Utilizes keyfiles where the file name is the key and the content is the value. Keyfile names are internally uppercase.
- **Sessions:** Contexts for key-value pairs, allowing the same key to have distinct values in different sessions.
- **Active Session:** Managed by a cursor file, determining the context for key operations.
- **Locked Keys:** Global keyfiles accessible across sessions, requiring specific functions for interaction.

### COMMANDS
- **Session Management:** Commands like `new`, `switch`, `lss` for handling sessions.
- **Keyfile Operations:** Commands like `write`, `read`, `delete` for managing keyfiles within the active session.
- **Locked Keyfile Operations:** Variants of standard commands (prefixed with `lock`) for managing locked keyfiles.
- **Utility Commands:** Includes `export`, `import`, `autoclean` for broader utility operations.

### OPTIONS
- **--admin, -A:** Administrative flag for sensitive operations.
- **--debug, -d:** Toggle debug output.
- **--envfile, -E:** For export/import commands, specifically for locked keyfiles.

### INSTALLATION
Set environment variables (`$FLAGX_HOME`, `$FLAGX_CURSOR_HOME`, `$FLAGX_EXPORT_PATH`) in your `.profile` or `.bash_profile`. Place the script in a local bin directory or symlink it.

### USAGE
Invoke commands using `kvfb` followed by the desired command and arguments. Examples include `kvfb write key value`, `kvfb switch session_name`, `kvfb ls_lock`, etc.

---

## Full Command Set Documentation

### Session Commands
- `kvfb new|n <session>`: Start a new session with a specified or random name.
- `kvfb switch|sw <session>`: Switch to an existing session.
- `kvfb lss|@`: List all available sessions.
- `kvfb curr <session>`: Return the ID of the active session.
- `kvfb reset|RRR`: Delete the active session.
- `kvfb nuke|nukeall|NNN`: Delete the entire filebase and start anew.
- `kvfb clean|CCC`: Remove all temporary keys from the session.

### Keyfile Commands
- `kvfb write|w <key> <value>`: Create a new key-value record in the active session.
- `kvfb write_safe|ws <key> <value>`: Create a new record only if the key does not exist; otherwise, rotate the old key.
- `kvfb new_key|key`: Create an empty keyfile placeholder.
- `kvfb read|r <key>`: Read the value of a key in the current session.
- `kvfb hist|h <key>`: Print the history of changes to a write-safe keyfile.
- `kvfb delete|rm <key>`: Delete a key from the current session.
- `kvfb toggle|tog <key>`: Toggle the boolean value of a key.
- `kvfb ls`: List all keyfiles in the current session.

### Locked (Protected) Keyfile Commands
- `kvfb write_lock|pw <key> <value>`: Write to a locked keyfile.
- `kvfb write_safe_lock|pws <key> <value>`: Write-safe operation for locked keyfiles.
- `kvfb new_lock|lock <key>`: Create a new locked keyfile.
- `kvfb read_lock|pr <key>`: Read a locked keyfile.
- `kvfb hist_lock|ph <key>`: History of a locked keyfile.
- `kvfb delete_lock|prm <key>`: Delete a locked keyfile.
- `kvfb toggle_lock|ptog <key>`: Toggle a locked keyfile's boolean value.
- `kvfb ls_lock|pls`: List all locked keyfiles.
- `kvfb promote <key> [--admin]`: Promote a session keyfile to global.
- `kvfb demote <key> [--admin]`: Demote a global keyfile to session-specific.

### Utility Commands
- `kvfb export [--envfile|-E]`: Export sessions and global keys.
- `kvfb import <filepath>`: Import a valid flagbase file.
- `kvfb autoclean`: Set to automatically remove temporary keyfiles.
