## UNWANTED CHECK INCREASES GAS AMOUNT

### Description:

There is a check at https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L385 which checks
`globalRewards != 0 && userRewards != 0`  , we know that globalRewards is always greater than or equal to userRewards and therefore
we can simplify this check to just `userRewards != 0` 

## ITERATING OVER THE REWARDS ARRAY COSTS HIGH GAS

### Description:

Here https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L163-L166 we are iterating through all the 
reward tokens to see if the reward token we want to add is already present in the array , in the worst case it will iterate through the whole
array , what we can do instead is , is in the ProducerToken struct at https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L19 add a mapping like isRewardToken , which sets a reward token to true if the reward token 
is listed/added to the rewardTokens array and then we can reduce the check from a for loop to just `if(isRewardToken)`

## <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

### Description:

Using the addition operator instead of plus-equals saves 113 gas

Instances:
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L90
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L119
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L124
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PxERC20.sol#L85
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/PxGmxReward.sol#L95
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L361

## MULTIPLE ADDRESS/ID MAPPINGS CAN BE COMBINED INTO A SINGLE MAPPING OF AN ADDRESS/ID TO A STRUCT, WHERE APPROPRIATE

### Description:

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined.
Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally,
if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

Instances:
Mappings at https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L23 and https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L31 can be considered for merge.

## USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS

### Description:

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, 
which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional 
MLOAD rather than a cheap stack read. Instead of declaring the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incurring the Gcoldsload for the fields actually read. 
The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, 
is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct.

Instances:
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L160
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L216
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L386



