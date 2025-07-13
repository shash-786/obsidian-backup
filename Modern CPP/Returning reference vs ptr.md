References are a different way of thinking. Think of references as "pointers to existing objects". Once you do that, you'll understand why they can't be NULL - the object exists and the reference points to it.

Therefore, if your function returns a reference to something that it creates, it needs to guarantee that it actually does create a valid object. If it does not, or is unable to, then that is grounds to throw an exception.

Contrast that with a pointer. A pointer can be NULL and the caller will have to deal with a NULL return value. Therefore, if your function cannot guarantee that it will return a valid reference and you don't want to throw exceptions, you will need to use pointers.