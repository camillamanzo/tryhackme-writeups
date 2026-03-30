# Linux Shells & Bash Scripting

## 🐚 Shell Basics

### Available Shells

| Shell | Description |
|-------|-------------|
| **Bash** | Default shell. Supports tab completion for commands |
| **Fish** | Friendly Interactive Shell — beginner friendly, has auto spell correction |
| **Zsh** | Z Shell — advanced tab completion, auto spell correction, highly customisable. Can be slow |

### Shell Commands

| Command | Description | Example |
|---------|-------------|---------|
| `pwd` | Print working directory | `pwd` |
| `grep` | Search for entries in a file | `grep "Text" file.txt` |
| `echo $SHELL` | Output which shell you're currently using | `echo $SHELL` |
| `cat /etc/shells` | Print all available shells on the current Linux OS | `cat /etc/shells` |
| `chsh -s /usr/bin/ShellName` | Change the default shell | `chsh -s /usr/bin/bash` |
| `bash` / `zsh` / `fish` | Switch to a different shell in the current session | `zsh` |
| `history` | Display all previous commands (Bash/Zsh) | `history` |

---

## 📝 Bash Scripting

### Basics

| Concept | Description | Example |
|---------|-------------|---------|
| `.sh` | Default file extension for bash scripts | `script.sh` |
| `nano script_name.sh` | Create a new script file | `nano myscript.sh` |
| `#!/bin/bash` | Shebang — must be the first line of every bash script | `#!/bin/bash` |
| `read` | Take input from the user | `read name` |
| `$variableName` | Reference a variable | `echo $name` |
| `#` | Comment — ignored during execution | `# this is a comment` |
| `chmod +x script_name.sh` | Give the script execute permissions | `chmod +x myscript.sh` |
| `./script_name.sh` | Execute the script | `./myscript.sh` |

---

### 📌 Script Examples

#### Basic input/output
```bash
#!/bin/bash
echo "Hi! What is your name?"
read name
echo "Welcome $name"
```

---

#### Loop — output numbers 1 to 10
```bash
#!/bin/bash
for i in {1..10}
do
  echo $i
done
```

---

#### Conditional — check if name matches
```bash
#!/bin/bash
echo "What is your name?"
read name
if [ "$name" = "Camilla" ]; then
    echo "Welcome $name! Here is the secret: THM_Script!"
else
    echo "Sorry, you aren't authorised to access the secret."
fi
```

---

#### Comprehensive — loop + conditionals + multiple variables
```bash
#!/bin/bash

# Defining variables
name=""
company=""
pin=""

for i in {1..3}; do
    if [ "$i" -eq 1 ]; then
        echo "Name:"
        read name
    elif [ "$i" -eq 2 ]; then
        echo "Company:"
        read company
    else
        echo "PIN:"
        read pin
    fi
done

# Checking if user entered the right details
if [ "$name" = "camilla" ] && [ "$company" = "Castoli" ] && [ "$pin" = "1234" ]; then
    echo "Access granted"
else
    echo "Access denied"
fi
```

---

### 🔑 Key Syntax Reference

| Syntax | Description |
|--------|-------------|
| `[ "$var" = "value" ]` | String comparison |
| `[ "$var" -eq 1 ]` | Numeric comparison (equal) |
| `&&` | Logical AND — all conditions must be true |
| `\|\|` | Logical OR — at least one condition must be true |
| `if / elif / else / fi` | Conditional block structure |
| `for i in {1..N}` | Loop from 1 to N |
| `do / done` | Loop block structure |
