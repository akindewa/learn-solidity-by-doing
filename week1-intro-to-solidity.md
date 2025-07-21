##  Integer Types in Solidity

In Solidity, data types like *uint* and *int* are used to store numbers, but they differ in what kind of numbers they allow.

### uint : Unsigned Integer
 - Stands for Unsigned Integer.
 - Stores only positive numbers, including zero.
 - Shortcut for uint256

```bash 
  uint balance = -100  //not valid
  uint balance = 100  // valid
 ```



### int: Signed Integer
  - Stands for Signed Integer
  - Can store both positive and negative numbers.



  ```bash
      int balance = -100 // valid  
      int score = 100    // also valid
  ```

  ### Bit Sizes: uint8 to uint256

  Solidity gives us different “sized” integers to optimize storage and gas.

 ###  Unsigned Integers (`uint`)

| Type     | Range            | Max Value              |
|----------|------------------|------------------------|
| `uint8`  | 0 to 2⁸ - 1       | 0 to **255**           |
| `uint16` | 0 to 2¹⁶ - 1      | 0 to **65,535**        |
| `uint32` | 0 to 2³² - 1      | 0 to **4,294,967,295** |                 |
| `uint256`| 0 to 2²⁵⁶ - 1     | up to **10⁷⁷**        |

> `uint` is an alias for `uint256`.




---

###  Signed Integers (`int`)

| Type     | Range                      |
|----------|----------------------------|
| `int8`   | -128 to 127                |
| `int16`  | -32,768 to 32,767          |
| `...`    | `...`                      |
| `int256` | ~ -10⁷⁷ to +10⁷⁷           |

> `int` is an alias for `int256`.

---

###  Notes

- `uint` is used when **negative numbers are not needed**.
- `int` is used when **both negative and positive values** are possible.
- Smaller types (e.g., `uint8`) **save storage** and **reduce gas costs**, but use with care to avoid overflows.
- uint8 for ages
- uint256 for token balances



### Why does bit size matter?
  Using smaller types like uint8 or int16:
  - Saves storage space
  - Uses less gas
  - But: overflow or underflow can cause bugs or break contracts

  ```bash
    uint8 small = 255;  // Saves gas  
    uint256 big = 10**77; // Default = uint256  
  ```

  ```bash
     uint8 max = 255;  
     max += 1; // Now 0!  
     OVERFLOW!!!!
  ```


  ## Solidity Arrays 


## 🔹 What Is an Array?

An **array** is a data structure that stores multiple values of the same type. Think of it like a row of lockers: same shape, numbered, and accessed by position (index).

---

## 🔹 Types of Arrays

### 1. Fixed-Size Arrays

- Declared with a specific length.
- Length cannot be changed after creation.

```solidity
uint[3] fixedArray = [1, 2, 3];
```

 - Holds exactly 3 elements.
- You cannot push new elements.

### 2. Dynamic-Size Arrays

- No predefined length.
- Can grow or shrink using built-in methods.

```
   uint[] dynamicArray;
   dynamicArray.push(5);
```

- Use .push() and .pop()
- Length is flexible


### 🔹 Declaration Syntax

```
  <dataType>[] arrayName;         // Dynamic array
  <dataType>[size] arrayName;     // Fixed-size array
  <dataType>[] memory tempArray = new <dataType[]>(size); // Memory array
```

### 🔹 Storage vs Memory vs Calldata Arrays


### 🔹 Built-in Methods & Properties

 - push(value):  Adds a new element to the end (only for dynamic arrays in storage)

  ```
   myArray.push(42);
  ```
  - pop(): Removes the last element:

  ```
  myArray.pop();
  ```

  - length: Returns the current number of elements:
  ```
     uint len = myArray.length;
  ```

  - Index Access: Access an item by its position:
  ```
    uint value = myArray[2]; // Gets the 3rd item
  ```

  ### 🔹 Looping Through an Array

  ```
  for (uint i = 0; i < myArray.length; i++) {
    // Do something with myArray[i]
    //for loops
}
  ```

## 🔹 Multidimensional Arrays
  - Fixed inside Fixed
   ```
   uint[2][3] matrix; // 3 arrays of 2 uints each
   ```

   - Dynamic inside Fixed
   ```
   uint[][3] matrix; // 3 dynamic arrays
```

  - Fully Dynamic
  ```
  uint[][] matrix;
  matrix.push([1,2,3]);
  ```

  ### 🔹 Full Example
  ```
  pragma solidity ^0.8.0;

contract MyArray {
    uint[] public numbers;

    function addNumber(uint num) public {
        numbers.push(num);
    }

    function getNumber(uint index) public view returns (uint) {
        return numbers[index];
    }

    function getLength() public view returns (uint) {
        return numbers.length;
    }

    function removeLast() public {
        numbers.pop();
    }
}
```

## 🔹 Note:
   Best Practices
- Avoid very large arrays, on-chain storage is expensive.

- Use calldata for read-only external parameters.

- Use memory for local arrays in internal logic.

- Index access is cheap; dynamic resizing is not.

### 🔹 Summary 
- Arrays can be fixed or dynamic.

- Use .push(), .pop(), and .length for dynamic control.

- Understand memory vs storage vs calldata for proper gas management.

- Looping and multi-dimensional arrays follow logical patterns.


##  Solidity Mappings 

In Solidity, a **mapping** is like a high-speed address book or a vending machine that always returns something even if you never put it in.

---

## 🔹 What Is a Mapping?

A `mapping` is a key-value store for looking up data. Think of it like a `dictionary` in Python or an `object` in JavaScript.

```solidity
mapping(KeyType => ValueType) public myMap;
```
It associates a Key with a Value, and allows you to read, update, or delete data — but with rules.

### 🔹 Syntax

```
mapping(address => uint) public balances;
```

- Keys can be any primitive type: uint, address, bool, bytes32, etc.

- Keys cannot be mappings, structs, or arrays.

-  Values can be any type even another mapping or struct()

### 🔹 Key Properties of Mappings


### 🔹 Basic Example

```mapping(address => uint) public balances;

function updateBalance(uint _amount) public {
    balances[msg.sender] = _amount;
}

function getBalance(address _addr) public view returns (uint) {
    return balances[_addr];
}
```
### 🔹 Default Value Behavior

If accessing a key never set, it returns the default value:
```
  balances[0xAbc...123]; // returns 0 even if never set
```
- Mappings don't throw errors for missing keys — they return 0, false, or empty values based on the value type.


### 🔹 Nested Mappings

```
mapping(address => mapping(uint => bool)) public access;

function grantAccess(uint _id) public {
    access[msg.sender][_id] = true;
}
//track whether a user has access to a resource by ID.
```

### 🔹 Mapping with Structs

```
struct Profile {
    string name;
    uint age;
}

mapping(address => Profile) public userProfiles;

function setProfile(string memory _name, uint _age) public {
    userProfiles[msg.sender] = Profile(_name, _age);
}
//maping an address to a struct
```

### 🔹 Deleting from a Mapping
- a key can't be removed , but you can reset its value:
```
delete balances[msg.sender]; // sets it back to 0
```
### 🔹 Why You Can’t Iterate

Mappings don’t store keys. That means:
- You can’t loop through all entries.
- If you want to track keys, use an array or counter alongside your mapping.

```
address[] public users;
function addUser(address _user) public {
    if (balances[_user] == 0) {
        users.push(_user);
    }
    balances[_user] = 100;
}
```

### 🔹 Full Example: Simple Bank

```pragma solidity ^0.8.0;

contract SimpleBank {
    mapping(address => uint) public balances;

    function deposit() public payable {
        balances[msg.sender] += msg.value;
    }

    function withdraw(uint _amount) public {
        require(balances[msg.sender] >= _amount, "Not enough funds");
        balances[msg.sender] -= _amount;
        payable(msg.sender).transfer(_amount);
    }

    function getMyBalance() public view returns (uint) {
        return balances[msg.sender];
    }
}
```
### Summary
- Mappings are powerful — like mini databases on-chain.

- Can’t be looped, can’t be checked for existence, always return defaults.

- Use nested mappings for complex relationships.

- Use with structs to simulate objects or records.

Combine with arrays if you need a list of keys.







