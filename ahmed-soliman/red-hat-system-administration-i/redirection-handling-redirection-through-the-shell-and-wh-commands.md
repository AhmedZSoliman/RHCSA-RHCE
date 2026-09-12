# Redirection, Handling Redirection Through the Shell, and wh Commands

#### **Redirection in Linux**

* **Redirection:** Instead of showing the output on your screen, redirect it to a file.
* **File descriptors:**
  * `0` → input (default: keyboard)
  * `1` → output (default: screen)
  * `2` → error (default: screen)

> Normally, you see output on the screen. Seeing an error (exception) is considered abnormal.\
> If you want to redirect an error to a file, you must use the descriptor `2`.

* **Symbols for redirection:**
  * `<` → input
  * `>` → output and error
* **Separating output and error:**
  * `1>` or `>` → output
  * `2>` → error

***

#### **Examples of Redirection**

1. Redirect output to a file:

```bash
ls -l 1> result.txt
# Equivalent: ls -l > result.txt
# Stores output in result.txt (creates file if it doesn’t exist)
```

2. Redirect error to a file:

```bash
ls file22.txt 2> error.txt
# Only errors will be saved in error.txt
```

3. Redirect both output and error to separate files:

```bash
ls file22 file2 > result.txt 2> error.txt
```

4. Redirect both output and error to the same file:

```bash
ls file2 2>&1 > error.txt
```

5. **Overwriting vs Appending**

* `>` → overwrite the file
* `1>>` or `2>>` → append to the file

***

#### **Using `cat`**

* Default behavior: read input from a file, not the keyboard:

```bash
cat error.txt
# Equivalent to: cat < error.txt
```

* Using a here-document:

```bash
cat <<soliman > error.txt
# Takes input until the keyword "soliman" is reached and redirects it to error.txt
```

* Redirect input and output simultaneously:

```bash
cat < error.txt > file.txt
```

***

#### **File Type**

* Any file extension in Linux has no real meaning; the file type is determined by its content:

```bash
file file1.txt
```

***

#### **Pipe (`|`)**

* Take the output of one command and use it as input for another:

```bash
ls -lR / | less
```

* **`less` command:** view files page by page (`q` to quit)
* **`more` command:** similar to `less`

***

#### **`tee` Command**

* Displays output on the terminal **and** saves it to a file:

```bash
ls | tee result.txt  # overwrite mode
ls | tee -a result.txt  # append mode
```

***

#### **WH Commands**

* **`w`** → shows who is logged in, from which terminal, and what they are doing.
  * `pts` → pseudo-terminal, emulates physical access to the machine.
* **`who`** → shows logged-in users.
  * `:0` → first monitor/session, `:1` → second, `:2` → third, etc.
* **`who am i`** → shows your current session info.
* **`whatis ls`** → explains what the command does.
* **`whereis ls`** → shows binary location of the command.
* **`last`** → shows login history and user status.
* **`lastlog`** → shows the last login time for all users.
