# HashMap

<b>How does HashMap work in java?</b>

----------------------------------------------------------------------------------------------------------------
In java HashMap store values in keyvalue pair form.HashMap in java is implementation class of Map Interface.<br>

<b>HashMap is an array of Entry objects:</b><br>
Consider HashMap as just an array of objects.<br>
Have a look what this Object is:<br>

    static class Entry<K,V> implements Map.Entry<K,V> {
    final K key;
    V value;
    Entry<K,V> next;
    final int hash;
     ...
     }
Each Entry object represents keyvalue pair. Field next refers to other Entry object if a bucket has more than 1 Entry.<br>
<p>Sometimes it might happen that hashCodes for 2 different objects are the same. In this case 2 objects will be saved in one
bucket and will be presented as LinkedList. The entry point is more recently added object. This object refers to other object with
next field and so one. Last entry refers to null.</p>

When you create HashMap with default constructor

    HashMap hashMap = new HashMap();

Array is gets created with <b>size 16</b> and default <b>0.75 load balance</b>.<br>

<b>HashMap with diagram in java</b>

![alt tag](https://github.com/sendkumaranil/HashMap/blob/master/Hash_Map3.jpg)

<b>Adding a new keyvalue pair:</b><br>
<ol>
<li>Calculate hashcode for the key</li>
<li>Calculate position hash % (arrayLength‐1)) where element should be placed(bucket number)</li>
<li>If you try to add a value with a key which has already been saved in HashMap, then value gets overwritten.</li>
<li>Otherwise element is added to the bucket. If bucket has already at least one element a new one is gets added
and placed in the first position in the bucket. Its next field refers to the old element.</li>
</ol>
<br>
<b>What put() method actually does:</b>

Lets note down the steps one by one:

<b>Step1:</b><br>
<p>First of all, key object is checked for null. If key is null, value is stored in table[0] position. Because hash code for null is
always 0.</p>
<b>Step2:</b><br>
<p>Then on next step, a hash value is calculated using key’s hash code by calling its hashCode() method. This hash value
is used to calculate index in array for storing Entry object. JDK designers well assumed that there might be some poorly written
hashCode() functions that can return very high or low hash code value. To solve this issue, they introduced another hash()
function, and passed the object’s hash code to this hash() function to bring hash value in range of array index size.</p>
<b>Step3:</b><br>
<p>Now indexFor(hash, table.length) function is called to calculate exact index position for storing the Entry object.</p>
<b>Step4:</b><br>
<p>Here comes the main part. Now, as we know that two unequal objects can have same hash code value, how two
different objects will be stored in same array location [called bucket].
Answer is LinkedList. If you remember, Entry class had an attribute “next”. This attribute always points to next object in chain.
This is exactly the behavior of LinkedList.
So, in case of collision, Entry objects are stored in LinkedList form. When an Entry object needs to be stored in particular index,
HashMap checks whether there is already an entry?? If there is no entry already present, Entry object is stored in this location.
If there is already an object sitting on calculated index, its next attribute is checked. If it is null, and current Entry object
becomes next node in LinkedList. If next variable is not null, procedure is followed until next is evaluated as null.</p>

<p>What if we add the another value object with same key as entered before. Logically, it should replace the old value. How it is
done?</p> 
<p>Well, after determining the index position of Entry object, while iterating over LinkedList on calculated index, HashMap
calls equals method on key object for each Entry object. All these Entry objects in LinkedList will have similar hash code but
<b>equals()</b> method will test for true equality. If <b>key.equals(k)</b> will be true then both keys are treated as same key object. This will
cause the replacing of value object inside Entry object only.
In this way, HashMap ensure the uniqueness of keys.</p>

<b>How get() methods works internally?</b><br>
<b>Answer:</b> we already should know that the way key uniqueness is determined in put() method , same logic is applied in get()
method also. The moment HashMap identify exact match for the key object passed as argument, it simply returns the value
object stored in current Entry object.
If no match is found, get() method returns null.
