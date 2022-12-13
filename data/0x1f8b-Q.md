- [Low](#low)
    - [**1. Wrong comment**](#1-wrong-comment)
- [Non critical](#non-critical)
    - [**2. Dead code**](#2-dead-code)
    - [**3. Avoid hardcoded values**](#3-avoid-hardcoded-values)

# Low

## **1. Wrong comment**

In the following comment, it doesn't take into account that a valid EOA will return `success and data.length == 0`.

> if not implemented, or returns empty data, return empty string

Because is not checked that the address is a valid contract, it will return "" for EOA instead of FAULT the transaction. If you call the method `callAndParseStringReturn` with an EOA as a token, it will work.

**Affected source code:**

- [SafeERC20Namer.sol:60](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/lib/SafeERC20Namer.sol#L60)

---

# Non critical

## **2. Dead code**

`parseStringData` is not used and can be removed:

**Affected source code:**

- [SafeERC20Namer.sol:30-44](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/lib/SafeERC20Namer.sol#L30-L44)

## **3. Avoid hardcoded values**

It is not good practice to hardcode values, but if you are dealing with addresses much less, these can change between implementations, networks or projects, so it is convenient to remove these values from the source code.

**Affected source code:**

Use the selector instead of the raw value:

- [SafeERC20Namer.sol:78](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/lib/SafeERC20Namer.sol#L78)
- [SafeERC20Namer.sol:89](https://github.com/code-423n4/2022-12-caviar/blob/0212f9dc3b6a418803dbfacda0e340e059b8aae2/src/lib/SafeERC20Namer.sol#L89)

