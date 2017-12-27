```js

pragma solidity ^0.4.19;

contract Storage {    
    address public frontend_contract;
    address public owner;
    mapping(address => uint) public balances;
    
    uint256 public total_Supply;
    
    string public name;
    string public symbol;
    uint8 public decimals;

    function Storage()
    {
        owner = msg.sender;
    }
    
    function Load(address _frontend_contract) only_owner
    {
        frontend_contract = _frontend_contract;
    }
    
    function name() constant returns (string) { return name; }
    function symbol() constant returns (string) { return symbol; }
    function decimals() constant returns (uint8) {return decimals;}
    
    function totalSupply() constant returns (uint256 _supply)
    {
        return total_Supply;
    }

    function balanceOf(address _owner) constant returns (uint _balance)
    {
        return balances[_owner];
    }
    
    function set_balance(address _owner, uint256 _amount) only_frontend
    {
        balances[_owner] = _amount;
    }
    
    function set_supply(uint256 _amount) only_owner
    {
        totalSupply = _amount;
    }
    
     /** DEBUGGING FUNCTIONS **/
    
    modifier only_frontend
    {
        if(msg.sender != frontend_contract)
        {
            throw;
        }
        _;
    }
    
    modifier only_owner
    {
        assert(msg.sender == owner);
        _;
    }
}
```
