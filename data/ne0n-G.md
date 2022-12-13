1) Internal functions only called once can be made inline to save gas

The following function `_validateTokenIds` is only called once by the `wrap` function. This function can thus be implemented inside the `wrap` function itself to save gas.

Justification: Suppose function A has a call to function B. If function A takes 100 gas and function B takes 50 gas, then the total gas will be 100+50+10(for making the call..example value) = 160

But if function B is implemented inside function A the it will take only 150 gas
One could verify this with `forge test --gas-report` command

Currently `wrap` takes on average 119936 and median 119783
After inlining the `_validateTokenIds` function, the average is 119892 and median is 119739

The numbers could vary , but the important thing is the difference.