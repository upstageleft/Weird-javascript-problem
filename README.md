
Possible scoping or referencing problem???

QUESTION: How do I prevent the global (and the closure) from being affected by operations in the local scope?

In the table below, the first column reflects the value of a global variable populated by a value copied from a matrix 
array in a closure by means of a helper function.

The second line of the table shows that the global value has changed when it should not have. The value of the specified 
cell of the matrix has been copied to a new variable, and that variable has been manipulated, but the result of that 
manipulation is somehow copied back to both the global variable and the matrix variable!

globVal initial value: 1,1 ←(should be constant) — globVar's scope: [object Window] — myObj: { oSrc: "1,1", oX: 2 }

        |`globVal[0] (global)`|    Operation:                 |`myVal[0] (local)`|`newVal (local)`| map.see(1,1) (closure)
--------|---------------------|-------------------------------|------------------|----------------|-------------------------
Before:|1|newVal = myVal[0] * myObj.oX|1|2|1,1
After:|2|myVal[0] = newVal|2|2|2,1

Here is the code performing the operations, along with a helper function to pull values from the matrix and the object 
containing the data value to be applied in the operations.

CODE BLOCK A

`function doStuff(){
    var myVal = mxGet( myObj.oSrc );
    var newVal = myVal[0] * myObj.oX;
    myVal[0] = newVal; // weirdness happens here!
}
 
function mxGet( xy ){
    var x_y = xy.split(',');
    return map.see( x_y[0], x_y[1] );
}

myObj = {
    oSrc: '1,1',
    oX: 2
}
`	

Here are the onload function which initializes the global and the closure which stores the matrix.

CODE BLOCK B

`function init(){
    globVal = mxGet('1,1'); // global declared here
    that = this;
    doStuff();
}
 
map = (function(){
    myHiddenMatrix = [ [ [0,0], [0,1], [0,2] ],
                        [ [1,0], [1,1], [1,2] ],
                       [ [2,0], [2,1], [2,2] ] ];
    return {
        pin: function( x, y, val ){ myHiddenMatrix[ x ][ y ] = val; },
        see: function( x, y ){ return myHiddenMatrix[ x ][ y ]; }
    }
})();
`	
