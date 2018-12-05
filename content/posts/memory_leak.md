---
title: "Memory leak"
date: 2018-12-05T15:47:36+02:00
---

So I tried to simulate the notorius memory leak with free pointers in C++. This is my code

```

#include <algorithm>
#include <vector>
#include <stdlib.h>
#include <memory>

auto RandomNumberGenerator = []()
{
    return rand() / RAND_MAX; 
};

struct S
{
    S(int s):data(s)
    {
        std::generate(data.begin(), data.end(), RandomNumberGenerator);
    }
    std::vector<double> data;
};

void leak(int x)
{
    // allocate on the free store
    S *s = new S(x);
    S sc = *s;

    // or allocate on stack
    //S s_stack(x);
    
    // or use a smart pointer
    //std::unique_ptr<S> sm{new S(x)};
}

int main()
{
    for(int t = 0; t < 100000; ++t)
    {
        leak(1000000);
    }
    return 0;
}

```
and this is my memory when allocating on the free store (am I the only one feeling excited about this?)

![leak](/img/leak.png)
![run_leak](/img/run_leak.png)

When I killed it the memory was already full and it had started dumping doubles on my hard drive!

Now, using the code in the comments, this is what happens when I avoid a pointer, or even more so when I use a smart
pointer! 

![smart_pointer](/img/smart_pointer.png)

Not a single byte dropped.


