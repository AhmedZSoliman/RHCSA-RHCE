# Scripting

&#x20;\
Bash is the default shell in Linux and has scripting capabilities.

**Programming languages**

**Bash Script**

&#x20;

&#x20;

text

```
#!/bin/bash
COMMAND 1
COMMAND 2
-
-
if /then/fi
-
-
while/do/done
-
-
```

text

```
chmod +x script
```

**Running scripts**

**Script Execution**\
`./script` `script` `/usr/bin/script`

Example:

text

```
[root@localhost dolphin]# echo $PATH
/home/dolphin/.local/bin:/home/dolphin/bin:/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin
```

**Variables**

* Variable is a holder of value in memory.
* Variable is used when a specific data will be called often for further processing.
* Variables can be used in command line interface or inside a script.

&#x20;

&#x20;

**Variable assignment**

`VARIABLE=VALUE`

Example:\
`x=5`

`x` is the variable.\
`$x` is the value of it.

`echo x` → `x`\
`echo $x` → `5`

**Command substitution**

text

```
[root@localhost dolphin]# x=$(date)
[root@localhost dolphin]# echo $x
Sun Sep 19 18:19:47 EET 2021
```

text

```
[root@localhost dolphin]# tar -czf mydailybackup_$(date +%s).tar.gz /etc
tar: Removing leading '/' from member names
[root@localhost dolphin]# ll mydailybackup_1632069250.tar.gz
-rw-r--r--. 1 root root 6205414 Sep 19 18:34 mydailybackup_1632069250.tar.gz
[root@localhost dolphin]#
```

**Variable Manipulation**

**Calling a variable best practice**

text

```
[root@localhost dolphin]# echo ${x}
Sun Sep 19 18:19:47 EET 2021
```

**Disabling `$` effect:**

&#x20;

&#x20;

text

```
[root@localhost dolphin]# echo \$x
$x
```

**Shell Variables**

Shell variables do not persist across shells:

text

```
[root@localhost home]# x=5
[root@localhost home]# echo $x
5
[root@localhost home]# su dolphin
[dolphin@localhost home]# echo $x

[dolphin@localhost home]#
```

**Set to print shell variables:**

text

```
[root@localhost dolphin]# set | grep x=5
x=5
[root@localhost dolphin]#
```

text

```
[dolphin@localhost ~]$ set | grep x=5
[dolphin@localhost ~]$
```

**Environment Variables**

Shell variable `______` `export x=5` → Environment variable

text

```
[root@localhost dolphin]# env
...
LOGNAME=dolphin
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
XDG_RUNTIME_DIR=/run/user/1000
PATH=/home/dolphin/.local/bin:/home/dolphin/bin:/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin
HISTSIZE=1000
GJS_DEBUG_TOPICS=JS ERROR:JS LOG
...
```

**Conditional statements**

&#x20;

&#x20;

A sequential script runs all the commands, one by one till the end. Conditional statements are used in order to run parts of the scripts based on a certain condition.\
`IF` statements are one of the most popular conditional statements.

**`if/then/fi`**

text

```
if <CONDITION>; then
    <STATEMENT>
    ...
    <STATEMENT>
fi
```

Condition → True → Statement(s) → `fi`\
→ False → (skip) → `fi`

**`if/then/else/fi`**

text

```
if <CONDITION>; then
    <STATEMENT>
    ...
    <STATEMENT>
else
    <STATEMENT>
    ...
    <STATEMENT>
fi
```

Condition → True → Statement(s) → `fi`\
→ False → Statement(s) → `fi`

&#x20;

&#x20;

**`if/then/elif/then/else/fi`**

text

```
if <CONDITION>; then
    <STATEMENT>
    ...
    <STATEMENT>
elif <CONDITION>; then
    <STATEMENT>
    ...
    <STATEMENT>
else
    <STATEMENT>
    ...
    <STATEMENT>
fi
```

Condition 1 → True → Statement(s) → `fi`\
→ False → Condition 2 → True → Statement(s) → `fi`\
→ False → Statement(s) → `fi`

**`case`**

text

```
case (variable) in
PATTERN1)
    <STATEMENT>
    ...
    ;;
PATTERN2)
    <STATEMENT>
    ...
    ;;
PATTERN3)
    <STATEMENT>
    ...
    ;;
    ...
esac
```

Switch → PATTERN1 → Statement(s) → `esac`\
→ PATTERN2 → Statement(s) → `esac`\
→ PATTERN3 → Statement(s) → `esac`

&#x20;

&#x20;

## **Iteration statements / loops**

Without loops (repetitive):

text

```
if <CONDITION>; then;fi
if <CONDITION>; then;fi
if <CONDITION>; then;fi
if <CONDITION>; then;fi
if <CONDITION>; then;fi
if <CONDITION>; then;fi
if <CONDITION>; then;fi
```

Condition → Statement(s) → End

***

### **`while` loop**

text

```
#!/bin/bash
x=0
while [ ${x} -lt 5 ]
do
    echo ${x}
    x=$((${x} + 1))
done
```

OR

`x=0; while [ ${x} -lt 5 ]; do echo ${x}; x=$((${x} +1)); done`

&#x20;

&#x20;

## **`for` loop**

text

```
for VARIABLE in LIST;
do
    COMMAND $VARIABLE
done
```

Example:

text

```
#!/bin/bash
for var in counter{1..10}
do
    echo $var
done
```

OR

`for var in counter{1..10}; do echo $var; done`

&#x20;
