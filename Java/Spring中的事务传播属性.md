## Spring事务传播属性（Propagation）：

1. REQUIRED（默认属性）  
如果存在一个事务，则支持当前事务。如果没有事务则开启一个新的事务。 
被设置成这个级别时，会为每一个被调用的方法创建一个逻辑事务域。如果前面的方法已经创建了事务，那么后面的方法支持当前的事务，如果当前没有事务会重新建立事务。  

    比如说，ServiceB.methodB的事务级别定义为PROPAGATION_REQUIRED,
    那么由于执行ServiceA.methodA的时候,ServiceA.methodA已经起了事务，这时调用ServiceB.methodB，ServiceB.methodB看到自己已经运行在ServiceA.methodA的事务内部，就不再起新的事务。而假如ServiceA.methodA运行的时候发现自己没有在事务中，他就会为自己分配一个事务。
    这样，在ServiceA.methodA或者在ServiceB.methodB内的任何地方出现异常，事务都会被回滚。即使ServiceB.methodB的事务已经被提交，但是ServiceA.methodA在接下来fail要回滚，ServiceB.methodB也要回滚 

2. MANDATORY   
支持当前事务，如果当前没有事务，就抛出异常。  
也就是说，他只能被一个父事务调用。否则，他就要抛出异常 

3. NEVER   
以非事务方式执行，如果当前存在事务，则抛出异常。 
 
    不能在事务中运行。假设ServiceA.methodA的事务级别是PROPAGATION_REQUIRED，
    而ServiceB.methodB的事务级别是PROPAGATION_NEVER,那么ServiceB.methodB就要抛出异常了。 

4. NOT_SUPPORTED   
以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。  

    当前不支持事务。比如ServiceA.methodA的事务级别是PROPAGATION_REQUIRED
    ，而ServiceB.methodB的事务级别是PROPAGATION_NOT_SUPPORTED，
    那么当执行到ServiceB.methodB时，ServiceA.methodA的事务挂起，而他以非事务的状态运行完，再继续ServiceA.methodA的事务。 

5. REQUIRES_NEW   
新建事务，如果当前存在事务，把当前事务挂起。  
  
    比如我们设计ServiceA.methodA的事务级别为PROPAGATION_REQUIRED，ServiceB.methodB的事务级别为PROPAGATION_REQUIRES_NEW，
    那么当执行到ServiceB.methodB的时候，ServiceA.methodA所在的事务就会挂起，ServiceB.methodB会起一个新的事务，等待ServiceB.methodB的事务完成以后，
    他才继续执行。他与PROPAGATION_REQUIRED的事务区别在于事务的回滚程度了。因为ServiceB.methodB是新起一个事务，那么就是存在
    两个不同的事务。如果ServiceB.methodB已经提交，那么ServiceA.methodA失败回滚，ServiceB.methodB是不会回滚的。如果ServiceB.methodB失败回滚，
    如果他抛出的异常被ServiceA.methodA捕获，ServiceA.methodA事务仍然可能提交。 

6. SUPPORTS  
支持当前事务，如果当前没有事务，就以非事务方式执行。  
    
    如果当前在事务中，即以事务的形式运行，如果当前不再一个事务中，那么就以非事务的形式运行

7. NESTED  
支持当前事务，新增Savepoint点，与当前事务同步提交或回滚。   
嵌套事务一个非常重要的概念就是内层事务依赖于外层事务。外层事务失败时，会回滚内层事务所做的动作。而内层事务操作失败并不会引起外层事务的回滚。 
