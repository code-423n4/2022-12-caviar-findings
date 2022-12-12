QA1: https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/lib/SafeERC20Namer.sol#L59
We need to check that ``selector`` can only be ``name()`` or ``symbol()``:
```
require(selector == 0x95d89b41 || selector == 0x06fdde03, "selector is not name() or symbol()");
```