# Test 
---
### String Test 
| Operator      | Purpose                    |
| ------------- | -------------------------- |
| `-z "string"` | String has zero length     |
| `-n "string"` | String has non-zero length |

### String Comparison
| Operator        | Purpose                                  |
| --------------- | ---------------------------------------- |
| `==` (also `=`) | String equality                          |
| `!=`            | String inequality                        |
| `<`             | String lexicographic comparison (before) |
| `>`             | String lexicographic comparison (after)  |
| `=~`            | String regular expression match          |

### File Test 
| Operator  | Purpose                                             |
| --------- | --------------------------------------------------- |
| `-a file` | Exists (use `-e` instead)                           |
| `-b file` | Exists and is a block special file                  |
| `-c file` | Exists and is a character special file              |
| `-d file` | Exists and is a directory                           |
| `-e file` | Exists                                              |
| `-f file` | Exists and is a regular file                        |
| `-g file` | Exists and is set-group-id                          |
| `-h file` | Exists and is a symbolic link                       |
| `-k file` | Exists and its sticky bit is set                    |
| `-p file` | Exists and is a named pipe (FIFO)                   |
| `-r file` | Exists and is readable                              |
| `-s file` | Exists and has a size greater than zero             |
| `-t fd`   | Descriptor fd is open and refers to a terminal      |
| `-u file` | Exists and its set-user-id bit is set               |
| `-w file` | Exists and is writable                              |
| `-x file` | Exists and is executable                            |
| `-O file` | Exists and is owned by the effective user id        |
| `-G file` | Exists and is owned by the effective group id       |
| `-L file` | Exists and is a symbolic link                       |
| `-S file` | Exists and is a socket                              |
| `-N file` | Exists and has been modified since it was last read |

### File Comparison 
| Operator          | Purpose                                                                                        |
| ----------------- | ---------------------------------------------------------------------------------------------- |
| `file1 -nt file2` | file1 is newer (according to modification date) than file2, or file1 exists and file2 does not |
| `file1 -ot file2` | file1 is older than file2, or file2 exists and file1 does not                                  |
| `file1 -ef file2` | file1 is a hard link to file2                                                                  |

### Boolean Algebra 
|Construct         |Effect                             |
|----------------- |---------------------------------- |
| `first` \| \| `second`| first _or_ second (short circuit)  |
| `first && second` | first _and_ second (short circuit) |
| `! condition`     | _not_ condition                    |