# Access Graph data using Gremlin Console

In this section, you will learn basic Gremlin to Access the Graph.

1. From your Cloud9 terminal, start the Gremlin Console again and start to connect to Neptune.

    ```
    :remote connect tinkerpop.server conf/neptune-remote.yaml
    :remote console
    ```

2. Add vertex with label and property.

    ```
    g.addV('person').property('name', 'justin')
    ```

    !!! Info ""
        The vertex is assigned a string ID containing a GUID. All vertex IDs are strings in Neptune.

3. Add a vertex with custom id.

    ```
    g.addV('person').property(id, '1').property('name', 'martin')
    ```

    !!! Info ""
        The `id` property is not quoted. It is a keyword for the ID of the vertex. The vertex ID here is a string with the number 1 in it.
        
        Normal property names must be contained in quotation marks.

4. Change property or add property if it doesn't exist.


    Here you are changing the `name` property for the vertex from the previous step. This removes all existing values from the `name` property.

    ```
    g.V('1').property(single, 'name', 'marko')
    ```

    !!! Info ""
        If you didn't specify "single", it instead appends the value to the name property if it hasn't done so already.

5. Add property, but append property if property already has a value.

    ```
    g.V('1').property('age', 29)
    ```

    !!! Info ""
        Neptune uses set cardinality as the default action.
        
        This command adds the `age` property with the value `29`, but it does not replace any existing values.
        
        If the `age` property already had a value, this command appends `29` to the property. For example, if the `age` property was `27`, the new value would be `[ 27, 29 ]`.

6. Add multiple vertices.
   
    You can send multiple statements at the same time to Neptune. 

    Statements can be separated by newline ('\n'), spaces (' '), semicolon ('; '), or nothing (for example: `g.addV(‘person’).next()g.V()` is valid).
    
    ```
    g.addV('person').property(id, '2').property('name', 'vadas').property('age', 27).next()
    g.addV('software').property(id, '3').property('name', 'lop').property('lang', 'java').next()
    g.addV('person').property(id, '4').property('name', 'josh').property('age', 32).next()
    g.addV('software').property(id, '5').property('name', 'ripple').property('ripple', 'java').next()
    g.addV('person').property(id, '6').property('name', 'peter').property('age', 35)
    ```

    !!! Info ""
        The Gremlin Console sends a separate command at every newline ('\n'), so they are each a separate transaction in that case. This example has all the commands on separate lines for readability. Remove the newline ('\n') characters to send it as a single command via the Gremlin Console.
        
        All statements other than the last statement must end in a terminating step, such as `.next()` or `.iterate()`, or they will not run. The Gremlin Console does not require these terminating steps.
        
        All statements that are sent together are included in a single transaction and succeed or fail together.

7. Add edges.

    ```
    g.V('1').addE('knows').to(g.V('2')).property('weight', 0.5).next()
    g.addE('knows').from(g.V('1')).to(g.V('4')).property('weight', 1.0) 
    ```

8. Delete a vertex.
   
    Removes the vertex with the `name` property equal to `justin`.

    ```
    g.V().has('name', 'justin').drop()
    ```

## Run a traversal.

1. Returns all person vertices.

    ```
    g.V().hasLabel('person')
    ```


2. Run a Traversal with values (valueMap()).
    
    Returns key, value pairs for all vertices that marko “knows.”
    ```
    g.V().has('name', 'marko').out('knows').valueMap()
    ```


4. Specify multiple labels.

    Neptune supports multiple labels for a vertex. When you create a label, you can specify multiple labels by separating them with `::`. This example adds a vertex with three different labels.

    ```
    g.addV("Label1::Label2::Label3") 
    ```

    !!! Info ""
        - The hasLabel step matches this vertex with any of those three labels: hasLabel("Label1"), hasLabel("Label2"), and hasLabel("Label3").

        - The `::` delimiter is reserved for this use only.

        - You cannot specify multiple labels in the hasLabel step. For example, hasLabel("Label1::Label2") does not match anything.

5.  Specify Time/date.

     ```
     g.V().property(single, 'lastUpdate', datetime('2018-01-01T00:00:00'))
     ```

    !!! Info ""
         Neptune does not support Java Date. Use the datetime() function instead. datetime() accepts an ISO8061-compliant datetime string.
         
         It supports the following formats: `YYYY-MM-DD`, `YYYY-MM-DDTHH:mm`, `YYYY-MM-DDTHH:mm:SS`, and `YYYY-MM-DDTHH:mm:SSZ`.

6. Here are several drop examples. Delete vertices, properties, or edges.

    ```
    g.V().hasLabel('person').properties('age').drop().iterate()
    g.V('1').drop().iterate()
    g.V().outE().hasLabel('created').drop()
    ```

    !!! Note
        The .next() step does not work with .drop(). Use .iterate() instead.

 
   





