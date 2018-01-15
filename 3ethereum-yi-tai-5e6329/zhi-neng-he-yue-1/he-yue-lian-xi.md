這邊我們再來練習一個教室合約

```js
pragma solidity ^0.4.19;

contract Classroom {
    
    address public teacher;
    Student[] public students; 
    struct Student {
        string name;
        uint weight;
        uint height;
        uint age;
    }
    
    function Classroom () public {
        teacher = msg.sender;
    }
    
    modifier onlyTeacher() {
        require(msg.sender == teacher);
        _;
    }
    
    function add_student(string name, uint weight, uint height, uint age) public onlyTeacher {
        students.push(Student(name, weight, height, age));
    }
    
    function how_many_students() constant public returns(uint){
        return students.length;
    }
    
}
```



