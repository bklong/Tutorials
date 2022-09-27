## For Loops Tutorial ##

**for loops**: a method of iterating over a series of values and performing an operation on each value

create an item that is a list of fruit:

```
fruit_list="banana apple orange grape plum pear durian pineapple lychee"
```

this command created the variable `fruit_list`

when referring to a variable after defining it, place $ in front of it and optionally {}

```
echo "${fruit_list}"
```

The first command line assigned a list of fruit to the variable `fruit_list`

The second command line used `echo` to repeat back the list that is assigned to the variable `fruit_list` (it listed the fruit all at once in the original order)

in unix-bash, a for loop has four parts:

1. statement saying what items to loop over
2. `do` to note the beginning of the commands that we want to loop through 
3. the commands to repeat
4. `done` to indicate end of loop

create a loop that will print each of the fruit in `fruit_list` individually:

```
for fruit in ${fruit_list}
do
echo "${fruit}"
done
```
This produced the list of fruit (printed), but individually down a vertical column in the terminal.

To add an "s" and make the fruit plural:

```
for fruit in ${fruit_list}
do
echo "${fruit}s"
done
```

The curly brackets help distinguish the variable name (`fruit`) and the text we want to add (s).

**Exercise 1**

Make a list of fruits/veggies that includes potato, tomato, and peach. Then add "es" to the items to make them plural.

```
food_list="tomato potato peach"
for food in ${food_list}
do 
echo "${food}es"
done
```

result:
```
tomatoes
potatoes
peaches
```

**Using for loops to interact with files**

set up:

```
mkdir for_example
cd for_example
echo "this" > file1.txt
echo "is" > file2.txt
echo "silly" > file3.txt
```

Directory: for_example
Files in directory: file 1, 2, 3
Content of files: this, is, silly

```
myfiles=$(ls)
```

The above line runs ls and places the results in myfiles. $() indicates that the command is to be executed.

```
for file in ${myfiles}
  do
    cat ${file}
  done
  ```
  
  Output: 
  this
  is
  silly
  
  **Exercise 2**
  
  The above loop prints the *contents* of each file in myfiles.
  
  **Exercise 3** (unsure about this one)
  
  ```
  for file in ${myfiles}
  do
  echo {file} contains ${file}
  done
  ```
  
  **Nesting For Loops**
  
  ```
  fruit_list="banana apple orange grape plum pear durian pineapple"

for fruit in $fruit_list
  do
    for number in first second third fourth fifth
      do
        echo "$number $fruit"
      done # inner loop
    echo ""
    echo "next fruit type:"
  done
  ```
  
  

