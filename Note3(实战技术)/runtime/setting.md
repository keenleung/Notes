runtime 使用的时候报错:

`Too many arguments to function call ,expected 0,have3`

需要设置一下:

Build Setting--> Apple LLVM 6.0 - Preprocessing--> Enable Strict Checking of objc_msgSend Calls  改为 NO
