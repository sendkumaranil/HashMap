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

<br>
<b>What is the importance of hashCode() and equals() methods?</b>

<p>HashMap uses Key object hashCode() and equals() method to determine the index to put the key-value pair. These methods are also used when we try to get value from HashMap. If these methods are not implemented correctly, two different Key’s might produce same hashCode() and equals() output and in that case rather than storing it at different location, HashMap will consider them same and overwrite them.</p>

<p>Similarly all the collection classes that doesn’t store duplicate data use hashCode() and equals() to find duplicates, so it’s very important to implement them correctly. The implementation of equals() and hashCode() should follow these rules.</p>

    If o1.equals(o2), then o1.hashCode() == o2.hashCode()should always be true.
    If o1.hashCode() == o2.hashCode is true, it doesn’t mean that o1.equals(o2) will be true.

<b>Can we use any class as Map key?</b><br>

<p>We can use any class as Map Key, however following points should be considered before using them.</p>
<ul>
<li>If the class overrides equals() method, it should also override hashCode() method.</li>
<li>The class should follow the rules associated with equals() and hashCode() for all instances. Please refer earlier question for these rules.</li>
<li>If a class field is not used in equals(), you should not use it in hashCode() method.</li>
<li>Best practice for user defined key class is to make it immutable, so that hashCode() value can be cached for fast performance.
Also immutable classes make sure that hashCode() and equals() will not change in future that will solve any issue with mutability.</li>
</ul>
<b>When map is decided to resize?</b>
<p>When the size of map reached to its threashhold then it decided to rehash or resize</p>
 <p>how to calculate threashold</p>
 
 public HashMap() {
		 this.loadFactor = DEFAULT_LOAD_FACTOR;
         threshold = (int)(DEFAULT_INITIAL_CAPACITY * DEFAULT_LOAD_FACTOR);
         table = new Entry[DEFAULT_INITIAL_CAPACITY];
         init();
}

where default value of DEFAULT_LOAD_FACTOR=0.75 and DEFAULT_INITIAL_CAPACITY is 16.
So when map size will reach 12 then this will decide to resize of map.
see below new entry of element in map code implementation:

void addEntry(int hash, K key, V value, int bucketIndex) {
			Entry<K,V> e = table[bucketIndex];
			table[bucketIndex] = new Entry<K,V>(hash, key, value, e);
			if (size++ >= threshold)
				resize(2 * table.length);
}

<b>Resize</b>
Rehashes the contents of this map into a new array with a larger capacity. 
This method is called automatically when the number of keys in this map reaches its threshold. 
If current capacity is MAXIMUM_CAPACITY, this method does not resize the map, but sets threshold to Integer.MAX_VALUE. 
This has the effect of preventing future calls.

Parameters:
newCapacity the new capacity, MUST be a power of two; 
must be greater than current capacity unless current capacity is MAXIMUM_CAPACITY (in which case value is irrelevant).

   void resize(int newCapacity) {
         Entry[] oldTable = table;
         int oldCapacity = oldTable.length;
         if (oldCapacity == MAXIMUM_CAPACITY) {
             threshold = Integer.MAX_VALUE;
             return;
         } 
         Entry[] newTable = new Entry[newCapacity];
         transfer(newTable);
         table = newTable;
         threshold = (int)(newCapacity * loadFactor);
     }
	 
	 Transfers all entries from current table to newTable.
	 
	void transfer(Entry[] newTable) {
		Entry[] src = table;
        int newCapacity = newTable.length;
        for (int j = 0; j < src.length; j++) {
             Entry<K,V> e = src[j];
             if (e != null) {
                 src[j] = null;
                 do {
                     Entry<K,V> next = e.next;
                     int i = indexFor(e.hash, newCapacity);
                     e.next = newTable[i];
                     newTable[i] = e;
                     e = next;
                 } while (e != null);
             }
         }
    }

