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

