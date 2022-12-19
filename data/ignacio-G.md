USE MULTIPLE REQUIRE() STATMENTS INSTED OF REQUIRE(EXPRESSION && EXPRESSION && ...)
SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS
Instead of using the && operator in a single require statement to check multiple conditions, using multiple require statements with 1 condition per require statement (saving 3 gas per &) 
https://github.com/code-423n4/2022-12-caviar/blob/main/src/Pair.sol#L71