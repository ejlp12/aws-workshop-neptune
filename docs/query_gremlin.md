# Access Graph data using Gremlin Console

In this section, you will learn basic Gremlin to Access the Graph.

1. Add vertex with label and property.

    ```
    g.addV('person').property('name', 'justin')
    ```

    !!! Info 
        The vertex is assigned a string ID containing a GUID. All vertex IDs are strings in Neptune.

2. Add a vertex with custom id.

    ```
    g.addV('person').property(id, '1').property('name', 'martin')
    ```

    !!! Info 
        The `id` property is not quoted. It is a keyword for the ID of the vertex. The vertex ID here is a string with the number 1 in it.
        
        Normal property names must be contained in quotation marks.

3. Change property or add property if it doesn't exist.


    Here you are changing the `name` property for the vertex from the previous step. This removes all existing values from the `name` property.

    ```
    g.V('1').property(single, 'name', 'marko')
    ```

    !!! Info
        If you didn't specify "single", it instead appends the value to the name property if it hasn't done so already.

4. Add property, but append property if property already has a value.

    ```
    g.V('1').property('age', 29)
    ```

    !!! Info
        Neptune uses set cardinality as the default action.
        
        This command adds the `age` property with the value `29`, but it does not replace any existing values.
        
        If the `age` property already had a value, this command appends `29` to the property. For example, if the `age` property was `27`, the new value would be `[ 27, 29 ]`.

5. Add multiple vertices.
   
    You can send multiple statements at the same time to Neptune. 

    Statements can be separated by newline ('\n'), spaces (' '), semicolon ('; '), or nothing (for example: `g.addV(‘person’).next()g.V()` is valid).
    
    ```
    g.addV('person').property(id, '2').property('name', 'vadas').property('age', 27).next()
    g.addV('software').property(id, '3').property('name', 'lop').property('lang', 'java').next()
    g.addV('person').property(id, '4').property('name', 'josh').property('age', 32).next()
    g.addV('software').property(id, '5').property('name', 'ripple').property('ripple', 'java').next()
    g.addV('person').property(id, '6').property('name', 'peter').property('age', 35)
    ```

    !!! Info Note
        The Gremlin Console sends a separate command at every newline ('\n'), so they are each a separate transaction in that case. This example has all the commands on separate lines for readability. Remove the newline ('\n') characters to send it as a single command via the Gremlin Console.
        
        All statements other than the last statement must end in a terminating step, such as `.next()` or `.iterate()`, or they will not run. The Gremlin Console does not require these terminating steps.
        
        All statements that are sent together are included in a single transaction and succeed or fail together.

6. Add edges.

    Here are two different ways to add an edge.

    ```
    g.V('1').addE('knows').to(g.V('2')).property('weight', 0.5).next()
    g.addE('knows').from(g.V('1')).to(g.V('4')).property('weight', 1.0) 
    ```

    Add more edges:

    ```
    g.V('1').addE('created').to(g.V('3')).property('weight', 0.4).next()
    g.V('4').addE('created').to(g.V('5')).property('weight', 1.0).next()
    g.V('4').addE('knows').to(g.V('3')).property('weight', 0.4).next()
    g.V('6').addE('created').to(g.V('3')).property('weight', 0.2)
    ```

7. Delete a vertex.
   
    Removes the vertex with the `name` property equal to `justin`.

    ```
    g.V().has('name', 'justin').drop()
    ```

 
   





