# Thought Process
So in this Challenge we received a source java code after going through this code i understood that there is a target password given 
which is not the right one so we need to reverse it following the instructions inside the for loop, there can be two ways to do this 
## 1.manually solving using pen and paper which is not what i did at all.
## 2.writing a code that reverses the given string to the desired password i want,here is my code :
```

void unscramble(char *buffer, char *password) {
    int i;
    
    for (i = 0; i < 8; i++) {
        password[i] = buffer[i];
    }
    
    for (i = 8; i < 16; i++) {
        password[23-i] = buffer[i];
    }
    
    for (i = 16; i < 32; i += 2) {
        password[46-i] = buffer[i];
    }
    
    for (i = 31; i >= 17; i -= 2) {
        password[i] = buffer[i];
    }
    
    password[32] = '\0';
}

int main() {
    char target[] = "jU5t_a_sna_3lpm18gb41_u_4_mfr340";
    char password[33];
    
    unscramble(target, password);
    printf("Password: %s\n", password);
 
    
    return 0;
}


```
after running this code i got the target string aka the correct password.
# Flag : picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_1fb380}


