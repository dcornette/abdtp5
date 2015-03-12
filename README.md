## README FOR USERS:
 
1. Download and compile **JLex, CUP** packages. Add them to classpath variable. You can find these packages from the web, [http://www.cs.princeton.edu/~appel/modern/java/JLex/](http://www.cs.princeton.edu/~appel/modern/java/JLex/)
[http://www.cs.princeton.edu/~appel/modern/java/CUP](http://www.cs.princeton.edu/~appel/modern/java/CUP)
 
For convenience, these packages have already been included in **lib** directory.
           
2. Create a system variable **COMPONENT** pointing to the **Component** directory (i.e., base directory of the package).
 
3. Add **COMPONET/classes** to the classpath. CUP folder and JLex folder should also be added to the classpath.
 
For UNIX users, you can also use the file **queryenv (found in Component/)** to set up the environment, i.e., type “source queryenv”.
 
4. If in windows, use **build.bat** file to compile the system, if in UNIX, use **build.sh** file.
 
Note that when compiling with the latest Java compiler, the following message will show up:
Note: Some input files use unchecked or unsafe ope
Note: Recompile with -Xlint:unchecked for details.
 
These warning messages can be ignored.
 
5. The details about table schema are specified in file <tablename>.det.
 
            Use **RandomDB** class to generate serialized schema file **<tablename>.md** and the data file in text format **<tablename>.txt**. The command is
            **java RandomDB <tablename> <# of records>**
 
6. The format of the **<tablename>.det** file is as shown below.
----------
 
	<# of columns>
	<size of tuple>
	<attribute name> <data type> <range> <key type> <column size>
	 --------
	 --------
	 --------
 
data type is either INTEGER/STRING/REAL
range:
    For integers, and real, it considers the values between 0 and the range specified. For primary keys, provide range value greater than or equal to number of records.
    For strings, just specify number of characters you string need to contain.       
Key type:
    May be PK/FK/NK respectively for primary key, foreign key, not a key.  Use only integer data type for primary key.
 
Attribute size:
            It is number of bytes required for the attribute. Remember that in java, integer is 4 bytes, character is 2 bytes.
 
7. Once you had the database records in text format in **<table name>.txt** file, then convert the records into object format. Use
          **java ConvertTxtToTbl <table name>**
This command creates an object format of records in a file **<table name>.tbl**
 
8. This command creates another file **<table name>.stat** that gives the statistics of the table. i.e., number of distinct values for each column.  The format of this file is as below.
	<# of tuples>
	<#of distinct values for col 1> <for col 2> …………………
 
9. Then write you query in some file, say query.in, and the result is required in query.out file. Then run the command to execute your query.
	**java QueryMain query.in query.out**

## README FOR DEVELOPERS
 
            All the components are categorized into 4 different packages.
 
# 1. Utilities:
           
            All the classes required for representation of the data, tuple, and schema are considered utilities.
 
Data types:
The tuples and schema are stored as a java serialized objects. Therefore the data types should implement serializable interface. Currently, the system supports Integer, Float, and String data types using the corresponding classes in Java API. If you want to create more data types, you should provide similar methods.          
 
Tuple:
            The tuple class is an encapsulation of all the data values in a record. It is stored in a file as a serialized object. This class contains methods to compare, and join tuples, and to locate data elements within the tuple etc.
 
Attribute:
            Attribute class specifies the attribute type (Integer/float etc), and the table to which the attribute belongs, and the name of the attribute.  If more types of data are to be supported, the corresponding types should be included in this class. This also specifies whether an attribute is a primary key/ foreign key, if it is.
 
 
SQLQuery:
            This class represents the format of the SQL query returned by the parser. This includes projection list, from list, and condition list. There is also a group by clause in SQL syntax, but this is currently not used. You can try this, if you develop some aggregation operators. The condition list is again divided into 2 parts, selection condition list, join condition list.
 
Condition:
            Condition class is used to represent the join/ selection predicate. Various comparison operators like less than, greater than, not equal etc are supported.
For select condition, the LHS of condition is an Attribute, and the RHS of the condition is a String.  For Join Condition both LHS and RHS are Attribute type.
 
Batch:
            In Java , the main memory can not be accessed and therefore no control on the actual page size. However, the page is simulated using the Batch class. It indicates the page size by number of bytes. The page size is a static variable and is fixed once during the initialization of the query processor. Every time the Batch is used a constructor fixes the number of tuples for the Batch based on the size of the tuple. This is to be done when buffer pages are assigned to an operator.
 
RandNumb:
            This class is to generate random numbers. This is useful, if you are developing some randomized optimizer.
 
Note: Many of the classes mentioned above should implement serializable, in order to store it in a file as a serialized objects.
 
# 2. PARSER
 
            We have used JLex (http://www.cs.princeton.edu/~appel/modern/java/JLex/) and CUP (http://www.cs.princeton.edu/~appel/modern/java/CUP) to generate Scanner and parser for an SQL query.
 
Scanner.lex  file specifies input specification format for JLex
parser.cup file specifies input specification format for parser.
 
Please look at the above web sites to understand the working of these packages.
 
 
# 3. Operators
 
            This package contains the operators required to perform various operations involved in a query.
Operator:
            This is a base class. It specifies the functions required for all the operators. Every operator component should implement this class. This contains the method signatures for open, next, close. It also contains the schema of the result produced by an operator, and a variable to specify the operator type (select/ project/join).
 
OpType:
            This class enumerates the list of operator types (select, project, join etc) using static variables.
            You have to change this class based on the type of operators you want to support in your query processor.
 
JoinType:
            This class is similar to the above. It enumerates various alternative algorithms (components) available for join operation in your query processor.
 
Scan:
            This class provides a scan operation on underlying table. The base table is stored in a file <tablename>.tbl.  This class reads the tuples from base table and returns a Batch at a time.
 
Select:
            This class is used to apply selection operation. This supports single table selection, and next method returns a Batch of resulting tuple whenever it is called.
 
Project:
            The Project operator takes the projection list (Select clause of SQL Query) and returns the resultant tuples.
 
The above operators require the function to set base operators, and to set buffers etc.
 
Join:
            Applies join condition and returns the resultant joined tuples. Supports function to set base operators, buffers etc.
 
Sinple nested join:
 
            This class implements simple nested-loops join algorithm. 
 
For all the above components, clone function is implemented. It is especially useful during the optimization, where alternative plan is to be derived and they must be separate entities.
           
            You can look into the source code for more elaborate documentation.
 
Debug
            In this class you can find certain functions to print various items for debugging purpose.
 
# 4. Optimizer
 
            A randomized iterative improvement algorithm has been implemented. To know the details of the algorithm refer this paper (Yannis E. Ioannidis et al., Randomized algorithms for optimizing large join queries, in Proceedings of ACM Sigmod Conference, pp 312-322, 1990)
 
RandomIntialPlan:
            This class is used to construct an initial query tree plan from the query mentioned in SQLQuery format.
 
The procedure is as below
 
From “From List” identify all the base tables, and place a scan operator for these tables.
Place all these operators in a hash table.
Then go through “Select condition list”. The tables on which there is a select condition get the corresponding operator, apply new select operator and place the new operator in hash table
Similar process is applied for join condition
apply project operation and return that as root operator
 
In above steps 3, 4 to get the random plan, the conditions are selected in a random order. If randomness is required, the conditions can be selected from the list in specific order.
 
PlanCost:
            This class provides methods to calculate the cost of each operator and cumulative cost. The methods also calculate the statistics of various columns in the generated result. The cost is based on disk I/O model. And the values are assumed that uniformly distributed among the database table.
 
There is one getStatistics method for each type of operator. Within method there should be calculations of the cost for each implementation type of the operator.
 
RandomOptimizer:
            From initial plan, neighboring plan can be obtained by different choices: by changing a method for a node, applying associative rule, commutative rule. For details see the above mentioned paper. So this class implemented methods to get neighbor plan with different choices. Exactly at this point, the clone function is useful to differentiate new plan completely from the old plan.
            getOptimizedPlan is the main algorithm for selecting the optimized plan.
           
Once the optimized plan is generated, each join operator (node in tree) specifies the join method to be used. Therefore an execute plan is prepared by replacing the nodes with corresponding join method components. This is done in makeExecPlan function.
 
QueryMain:
            This is the main class that combines the parser, optimizer, execution plan, and result printing to file. Look into the source code, it is self explanatory.