---
layout    : post
title     : "Some useful C++ macroses for bit operations."
date      : 2020-01-23 11:15:05 +0200
categories: 
  - C++
---

Here is some useful C++ macroses for bit operations.

{% highlight cpp %}
/*
*  Extracts from x the bits specified by the bit mask m. Use it to tests whether any of the mask bits is set.
*/
#define BITS(x,m) \
    ((x)&(m))
/*
* Extracts from x the bits specified by the bit mask m, except those which are set in be.
*/
#define BITS_EXCEPT(x,m,be) \
    ((x)&(m))& ~(be)
/*
*   Extracts from x the bits specified by the bit mask m and compares the result with the 
*   bit pattern b.
*   This is equivalent to the expression (BITS(x,m)==(b)).
*   Use this macro to test whether the masked bits coincide with a particular bit pattern.
*   Special case: The call TEST_BITS(x,m,m) returns true only if all the bits set in m are 
*   also set in x.
*/
#define TEST_BITS(x,m,b) \
    (BITS(x,m)==(b))
/*
*   Is equivalent to the expression (BITS_EXCEPT(x,m,be)==(b)).
*   Use it to test whether the masked bits of x, except those which are set in be, coincide 
*   with a particular bit pattern.
*/
#define TEST_BITS_EXCEPT(x,m,b,be) \
    BITS_EXCEPT(x,m,be)==(b)
/*
*   Sets to 1 those bits of lvx which are set in b, leaving unchanged all others.
*   Use it to simultaneously set to 1 several flags in a bit-coded flagword.
*/
#define BITS_ON(lvx,by) \
    (lvx)|=(by)
/*
*   Sets to 1 those bits of lvx which are set in b but not in be, leaving unchanged all others.
*/
#define BITS_ON_EXCEPT(lvx,by,be) \
    (lvx)|=(by)&~(be)
/*
*   Sets to 0 those bits of lvx which are set in b, leaving unchanged all others.
*   Use it to simultaneously clear (reset to 0) several flags in a bit-coded flagword.
*/
#define BITS_OFF(lvx,bn) \
    (lvx)&= ~(bn)
/*
*   Sets to 0 those bits of lvx which are set in b but not in be, leaving unchanged all others.
*/
#define BITSOFF_EXCEPT(lvx,bn,be) \
    (lvx)&= ~((bn)&~(be))
/*
*   Toggles those bits of lvx which are set to 1 in b, leaving unchanged all 
*   others (the toggle operation is the same as XOR with 1).
*   Use this macro to simultaneously toggle several flags in a bit-coded flagword.
*/
#define BITS_TOGGLE(lvx,b) \
    (lvx)^= (b)
/*
*   Toggles those bits of lvx which are set in b but not in be, leaving unchanged all others.
*/
#define BITS_TOGGLE_EXCEPT(lvx,b,be) \
    (lvx)^= ((b)&~(be))
/*
*   Is equivalent to executing BITS_OFF(lvx,bn) and then BITS_ON(lvx,by).
*/
#define BITS_ON_OFF(lvx,by,bn) \
    {                          \
        BITS_OFF(lvx,bn);      \
        BITS_ON(lvx,by);       \
    } 
/*
*   Is equivalent to executing BITS_OFF_EXCEPT(x,bn,be) and then BITS_ON_EXCEPT(x,by,be).
*/
#define BITS_ON_OFF_EXCEPT(lvx,by,bn,be) \
    {                                    \
        BITS_OFF_EXCEPT(lvx,bn,be);      \
        BITS_ON_EXCEPT(lvx,by,be);       \
    }
{% endhighlight %}
