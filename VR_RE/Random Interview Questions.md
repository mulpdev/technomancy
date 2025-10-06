https://www.reddit.com/r/ExploitDev/comments/1cmyyft/interview_question/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button

```C
/*
Can this be exploited on x64 and x86? 

Is it exploitable with mitigations enabled, ASLR, DEP, Stack Canaries, CFG.
*/
void main()
{
    int var;
    void (*func)()=test;
    char buf[128];
    fgets(buf,140,stdin);
    func();
}
```

