## Accessing Neptune using Python

### Install PIP

1. Open your Cloud9 IDE, in the terminal run this commands:

    ```
    sudo pip install virtualenvwrapper
    echo "export WORKON_HOME=$HOME/.virtualenvs" >> ~/.bashrc
    echo "export PROJECT_HOME=$HOME/Devel" >> ~/.bashrc
    echo "source /usr/local/bin/virtualenvwrapper.sh" >> ~/.bashrc

    source ~/.bashrc
    mkvirtualenv neptunedemo --python=python3.6
    pip install chalice
    pip install gremlinpython
    ```

2. Create a file `neptune_test.py` and change ==`{your-neptune-endpoint}`== to your Neptune endpoint.

    ```
    from __future__  import print_function  # Python 2/3 compatibility

    from gremlin_python import statics
    from gremlin_python.structure.graph import Graph
    from gremlin_python.process.graph_traversal import __
    from gremlin_python.process.strategies import *
    from gremlin_python.driver.driver_remote_connection import DriverRemoteConnection

    graph = Graph()

    remoteConn = DriverRemoteConnection('wss://{your-neptune-endpoint}:8182/gremlin','g')
    g = graph.traversal().withRemote(remoteConn)

    print(g.V().limit(2).toList())
    remoteConn.close()
    ```

3. Run the program

    !!! Warning 
        Make sure Security Group used by Neptune instance are able to accept inbound traffic from Cloud9.
       
    ```
    python3 neptune_test.py
    ```

