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

/**
 * @title Basic token
 * @dev Basic version of StandardToken
 */
contract Logic {
    address public owner;
    
    address public lib;
    
    function Load(address _lib) only_owner
    {
        lib = _lib;
    }

    function Logic()
    {
        owner = msg.sender;
    }
    
    function() payable
    {
        lib.delegatecall(msg.data);
    }
    
    modifier only_owner
    {
        assert(msg.sender == owner);
        _;
    }
}
contract ERC223ReceivingContract
{
    function tokenFallback(address, uint256, bytes) {}
}

contract Lib {
    
    event Transfer(address indexed from, address indexed to, uint indexed value, bytes data);

    function transfer(address _to, uint _value, bytes _data)
    {
        /* Only hardcoded addresses could be `delegatecall`ed */
        address _storage = 0x873ad067fa8073c57e4a566583437e971a69a731;
        
        uint codeLength;
        assembly {
            codeLength := extcodesize(_to)
        }
        
        if(!_storage.call(bytes4(sha3("decrease_balance(address,uint256)")), msg.sender, _value))
        {
            throw;
        }
        if(!_storage.call(bytes4(sha3("increase_balance(address,uint256)")), _to, _value))
        {
            throw;
        }
        
        if(codeLength>0) {
            ERC223ReceivingContract receiver = ERC223ReceivingContract(_to);
            receiver.tokenFallback(msg.sender, _value, _data);
        }
        Transfer(msg.sender, _to, _value, _data);
    }
    
    function transfer(address _to, uint _value)
    {
        address _storage = 0x873ad067fa8073c57e4a566583437e971a69a731;
        
        uint codeLength;
        bytes memory _empty;

        assembly {
            codeLength := extcodesize(_to)
        }
        
        if(!_storage.call(bytes4(sha3("decrease_balance(address,uint256)")), msg.sender, _value))
        {
            throw;
        }
        if(!_storage.call(bytes4(sha3("increase_balance(address,uint256)")), _to, _value))
        {
            throw;
        }
        
        if(codeLength>0) {
            ERC223ReceivingContract receiver = ERC223ReceivingContract(_to);
            
            receiver.tokenFallback(msg.sender, _value, _empty);
        }
        Transfer(msg.sender, _to, _value, _empty);
    }
}
```
