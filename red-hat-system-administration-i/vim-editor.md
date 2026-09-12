# Vim Editor

## **Vim Editor**

* **Vim** is an **advanced text editor**.
* **Three modes in Vim**:
  1. **Command mode** → read-only & navigation
  2. **Insert mode** → modification
  3. **Execution mode**
* **How to move between modes**:

<figure><img src="../.gitbook/assets/image (133).png" alt=""><figcaption></figcaption></figure>

| From         | To             | Command / Key |
| ------------ | -------------- | ------------- |
| Command mode | Insert mode    | `i`           |
| Insert mode  | Command mode   | `Esc` key     |
| Command mode | Execution mode | `:`           |

***

#### **Commands in Command Mode**

| Command                     | Description                                       | Comments                            |
| --------------------------- | ------------------------------------------------- | ----------------------------------- |
| `i`                         | Enter insert mode                                 | Press `Esc` to exit Insert Mode     |
| `[Esc]`                     | Enter command mode                                | ESC will take you into Command Mode |
| `dd`                        | Delete line                                       | Ex) `2dd` - Deletes 2 lines         |
| `x`                         | Delete letter                                     |                                     |
| `dw`                        | Delete word                                       |                                     |
| `dG`                        | Delete everything until end of file               |                                     |
| `gg`                        | Jump to top of file                               |                                     |
| `G`                         | Jump to bottom of file                            |                                     |
| `yy`                        | Yank (copy) line                                  | Ex) `3yy` - Copies 3 lines          |
| `p`                         | Paste                                             |                                     |
| `u`                         | Undo                                              | Ex) `4u` - Undoes last 4 changes    |
| `Ctrl + r`                  | Redo                                              |                                     |
| `/[word]`                   | Search for a word                                 | Press `n` to continue search        |
| `%s/word/another_word/g`    | Replace all words                                 |                                     |
| `x,y s/word/another_word/g` | Replace from specific line to line                |                                     |
| `:%s/Hello/World/g`         | Search all words "Hello" and replace with "World" |                                     |
| `:5,12s/Hello/World/g`      | Replace only from line 5 to 12                    |                                     |

***

#### **Saving and Quitting (Execution Mode)**

| Command          | Description          | Comments                                  |
| ---------------- | -------------------- | ----------------------------------------- |
| `:w`             | Save the file        | Writes the changes                        |
| `:q`             | Quit                 | Will not quit if file has unsaved changes |
| `:q!`            | Quit without saving  |                                           |
| `:wq`            | Save and quit        | At the same time                          |
| `:set nu`        | Display line numbers |                                           |
| `:set nu!`       | Remove line numbers  |                                           |
| `:[line_number]` | Jump to a line       | Ex) `:23` - jumps to line 23              |

***
