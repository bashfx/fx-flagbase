

# FX-FLAGBASE(1)


## Bash 3.2+ Script
FX-FLAGBASE(1) is a utility for managing key-value pairs in environment profiles, using the filesystem as its primary storage. It separates variable context from execution context.

Useful in cases where runtime variables differ from user environment variables, it uses a local file system store for consistent value persistence across contexts, requiring the reading of a local keyfile.

It supports dynamic editing of variables in the environment profile without restarting the execution context, but can be used to build, copy, edit or move environment profiles around through its import and export functions.

## Terminology

### Keyfiles and KV filebase format 
In flagbase, information is stored in a `KV filebase format` (KVFB) which means the key is the name of the file (called a keyfile), and the content of the keyfile is the value. So in flagbase `x = 100` means there is a keyfile called `x` that has the contents or value of `100`. Internally, *all keyfile names* are treated as uppercase, regardless of how they are referenced by the user. 


### Sessions
A session provides context for any given key, so for example, when you start a new flagbase, the primary session is `main`. If you then add `x = 100` record to the session, then the key can be thought of as `x@main = 100`. That way if you create a new session `fun` and add the same `x = 999`, then you have both `x@main` and `x@fun` with their respective values.

### Active Session
The active session is maintained by the cursor file which is also in KVFB format; it simply stores the name of the session. Switching, creating or destroying a session will rotate or empty the active session cursor. An active session is required in order to create, edit or access any key.

### Locked (Protected) Keys
Locked keyfiles are KVFB format keyfiles that act as session-less globals, meaning their values are accessible independent of the active session. The trade-off is you must use the locked* variant of keyfile functions to access and update them. 

Internally, locked keyfiles are stored as __KEYFILE_NAME__, but are accessed by `keyfile_name`, the system makes no distinction between the two when using locked* functions.

Locked keyfiles are implemented at three levels, (1) intent locked (2) encoding locked (3) encrypted locked. The current implementation of flagbase only supports Level 1 Locked keyfiles.

### Other Keyfiles
You can also denote a temporary keyfile by naming it with a single leading underscore, `_KEYFILE`. 

Temporary keyfiles are deleted whenever any of the cleanup functions are called, or if the system `__AUTOCLEAN__` system keyfile is set to a true-ish value.



## Install

In your `$HOME` directory, update your `.profile` or `.bash_profile` depending on which your system loads on login with the following customizable variables:

`$FLAGX_HOME` -  prefix which will be the home directory for all files used by FLAGBASE. Set the variable in your profile, and the script will use this location as your filebase store.

`$FLAGX_CURSOR_HOME` - where the active cursor file is stored (defaults to $HOME/.flagx), the cursor file keeps track of the current context or session.

`$FLAGX_EXPORT_PATH` - where you want the script to output any exports (defaults to $HOME/exports)

Move the script into a local bin directory like `.local/bin` or wherever you store user shell scripts. If you like you can also symlink to a bin location and rename the executable, I usually rename a symlink to `kvfb`




## Usage 

### kvfb (command|alias...) <args...> [options...]
*using kvfb as a symlink name to the script*

## Options

### [ --admin | -A ] - requires admin flag
*administrative commands require an additional -A | --admin flag to execute, note there is no additional security this is just a speedbump so you dont accidentally overwrite something; while the script is in development admin flags may be disabled*

### [ --debug | -d ] - toggle debug output
*outputs additional debug messages to the console for testing*

### [ --envfile | -E ] - toggle debug output
*for use with the export and import commands, to denote locked keyfiles only*

## Usage - Setup Commands

### kvfb nuke|nukeall|NNN
*deletes the entire filebase and starts a new one*

### kvfb reset|RRR
*deletes the active session*

### kvfb clean|CCC
*removes all temporary keys from the session*


## Usage - Session Commands

### kvfb curr <session>
*return the cursor id of the active session, if it exists*

### kvfb new|n <session>
*start a new session context with specified name, or random name*

### kvfb switch|sw <session>
*move the active session cursor to the specified (existing) session name*

### kvfb lss|@ 
*list all available sessions*


## Usage - Keyfile Commands

### kvfb write|w <key> <value> 
*creates a new record in for the active session key=>value*

### kvfb write_safe|ws <key> <value> 
*create a new record, if a record does not already exist for the given key, else rotate the old key, to key.1*

### kvfb new_key|key
*creates an empty keyfile placeholder for the current session*

### kvfb read|r <key> 
*reads the value of a record, if any, from the current session*

### kvfb hist|h <key> 
*prints the history of changes to a write-safe keyfile, if a keyfile is overwritten with write the history is deleted*

### kvfb delete|rm <key> 
*delete the key of a record, if any, froinm the current session*

### kvfb toggle|tog <key> 
*if the key value is a toggle-able boolean, then toggle the value. This works for values set to 0,1 or true,false. Before you can begin to toggle a value it must have a boolean-like value*

### kvfb ls
*list all keyfiles available in the current session record*



## Usage - Locked (Protected) Keyfile Commands
*note that all implemented locked keyfile commands mirror their unlocked counterparts*

### kvfb write_lock|pw <key> <value> 
*same as write, but for locked keyfiles*

### kvfb write_safe_lock|pws <key> <value> 
*same as write_safe, but for locked keyfiles*

### kvfb new_lock|lock <key>
*same as write, but for locked keyfiles*

### kvfb read_lock|pr <key>
*same as read, but for locked keyfiles*

### kvfb hist_lock|ph <key> 
*same as hist, but for locked keyfiles*



### kvfb delete_lock|prm <key>
*same as delete, but for locked keyfiles*

### kvfb toggle_lock|ptog <key>
*same as toggle, but for locked keyfiles*

### kvfb ls_lock | pls
*list all locked keyfiles*


### kvfb promote <key> [--admin]
*promotes a session keyfile into a global key, by removing it from the local session store, placing it into the global store, and changing the internal keyfile format*

### kvfb demote <key> [--admin]
*demotes a global keyfile into a session key, by removing it from the global record store placing it into the active session store, and changing the internal keyfile format*


## Usage - Utility Commands

### kvfb export [ --envfile| -E ]
*export all the sessions and global keys to the export path, specifying the --envfile flag will only export the global keys*

### kvfb import <filepath> 
*import a valid flagbase file into the current session. must be in key=value format with global keys using their fully delimited names like: __LOCKED_KEY_NAME__ ; change the active session prior to impoting*

### kvfb autoclean
*set the system keyfile to automatically remove any temporary keyfiles on the next system startup*
