# Introduction #
The PHP serialization feature is currently in the **requirements** phase.

## Serialization Format ##
The serialization format for simple types will be:

| **Type**  | **Serialized**                | **Example**      |
|:----------|:------------------------------|:-----------------|
|NULL	      |N;	                            |N;                |
|Integer    |i:$data;	                      |i:123;            |
|Double     |d:$data;	                      |d:1.23;           |
|Float	     |d:$data;	                      |d:1.23;           |
|Boolean    |b:$bool\_value;                |b:1;              |
|String     |s:$str\_length:"$data";	       |s:5:"Hello"       |
|Array	     |a:$key\_count:{$key;$data}	    |a:1:{i:1;i:2}     |
|           |$data can be of any data type  |                  |

### Object Serialization ###

We need a bit more room to describe the way objects are serialized.
Take the following PHP\JS Object:

```
class TestClass {
    private   $_id = 123;
    protected $_types = array('php', 'js', 'xml');
    public    $publicId = 456;
	
    public function getPublicId() {
        return $this->publicId;
    }
    private function getId() {
        return $this->_id;
    }
    protected function getTypes() {
        return $this->_types;
    }
}
$s = new TestClass();
$ser = serialize($s);
var_dump($ser);
```

PHP serializes the object as:

`O:9:"TestClass":3:{s:14:"�TestClass�_id";i:123;s:9:"�*�_types";a:3:{i:0;s:3:"php";i:1;s:2:"js";i:2;s:3:"xml";}s:8:"publicId";i:456;}`

The format is:

`o:$class_name_len:$class_name:$member_len:{$members}`

Where, each $member in $members has the following format:

`s:$str_len:"$visibility_modifier$name":$value`

Where $value is one of the data types as already defined above and $visibility\_modifier is:

| **PHP modifier**| **Format**                | **Example**        |
|:----------------|:--------------------------|:-------------------|
|public           |$name                      |publicId            |
|protected        |`*`_$var\_name_|`*`_types_|
|private          |$class\_name_$var\_name_|TestClass\_types    |

## Concerns ##
There are a few initial concerns, for example, how to treat functions of an object in JavaScript as PHP has no such feature. A solution may be to wrap the function up in an instance variable with a consistent naming scheme, or to create another object out of it and again use a consistent naming scheme.

# Ideas\Suggestions\Comments #
Ideas are welcome, please comment!