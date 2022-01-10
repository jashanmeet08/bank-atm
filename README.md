# bank_atm
Repo for Bank ATM project in CS 611, Object Oriented Principles class at BU, Fall 2020. 
Clients can use the ATM to create accounts, withdraw/deposit/transfer money,
apply for loans, open a 401K retirement plan, and view their transactions. 
The ATM may also be used by a banker to see all users and accounts, as well
as consider loan applications. 

##### Authors:
- Navoneel Ghosh
- Sandra Zhen
- Nathan Lauer

Please feel free to ask us any questions!

##### Compilation Instructions:
To compile, please execute the following steps:
```bash
$ find ./src/main -name "*.java" > sources.txt 
$ javac @sources.txt
```

Note that we have already provided the sources.txt file, but feel free to regenerate it if desired
Further, note that there may be some warnings. These can be ignored.

##### Running the ATM
Once, compiled the program can be run as follows:
```bash
$ java -cp src main.Main
```

##### Usage 
The ATM can be used either as a Client or an Admin. 

To use the ATM as a Client:
- Once started, you'll need to create an account the first time. Click the sign up button, and following the instructions
- After signing up, you'll need to log in.
- After logging in, a main menu will appear, where options for accounts, transactions, and loans will appear.


To use the ATM as an Admin:
- We have provided a default admin account for the purposes of this project. To use it:
- login with username: admin and password: BankPass@611
- Once logged in, an admin menu appears.

##### Architecture
Please see the included pdf for the overview of the architecture. However, 
generally, the repository is split into gui and backend packages, and makes 
extensive use of design patterns, interfaces, and abstract classes to implement all
features.

##### GUI Design
All code for the frontend is contained in the gui package. This makes it easy to differentiate
between GUI code and backend code. Part of the fronted GUI code was generated by intellij,
as we used intellij's BUI builder for some of the windows.

##### Design Patterns
There are a number of design patterns that are used throughout this repository. 
This section contains a brief description of them.

###### Singleton Pattern:
There are a number of classes that are Singletons. These classes fall into three different cateogries:
- Currencies: Each currency is implemented as a Singleton, as there should only be a single
entity for each currency.
- ExchangeRateTable: There is one class that contains all exchange rates
- CollectionManagers: Each of the collection managers (described in more detail below) is implemented
as a Singleton, as there should only be one manager for each type of collection.

###### Factory Method/Abstract Factory
We use the Factory Method and Abstract Factory design patterns in order to construct
accounts. There is a primary factory called AccountFactory, and multiple factory subtypes
which build an account of the appropriate type. The primary factory invokes the correct
factory subtype depending on the type of Account that should be created. We also provide 
an interface AccountFactorCreator, which every account factory implements.

###### Strategy Pattern
The interest system is implemented using the strategy pattern. Some accounts can earn interest,
and those that do implement the interface InterestEarnable, which specifies that any such
account must be able to provide an InterestEarningExecutor. There are a number of InterestEarningExecutors,
specifically classes that represent interest compounded daily, monthly, and yearly. For any account
that earns interest, the associated factory will attach the correct InterestEarningExecutor in
the strategy pattern fashion. This allows for easy composition between accounts and the interest 
earning system.

##### Users
Class user is an abstract class, with concrete subtypes Client and Admin.

##### Transactions
We provide an abstract class Transaction, and four concrete subtypes. They are:
- Deposit
- Withdraw
- Transfer
- LoanPayment

Each of these concrete subtypes are individually responsible for the semantics of executing 
the specific transaction. The advantage here is that we can easily group all transactions under
one collection.

##### Accounts
We have a multiple level inheritance system for Accounts. It generally goes as follows:
There is an abstract class Account, with subclasses SavingsAccount, CheckingAccount, LoanAccount, and InvestmentAccount.
Each of these are themselves abstract classes. Then, we provide a number of concrete sublcasses: 
- basic checking account
- premium checking account: earns a small amount of interest
- low interest savings account
- high interest savings account
- generic loan account: earns high interest rate for the bank.
- 401K account

Securities Accounts: As of now, the only securities account we have is the 401K investment account. 
There are no particular rules about these types of Accounts at the moment. The idea is primarily
to demonstrate that this architecture easily expands to these types of accounts.

Loans: Loans are a specific type of Account, in which a the bank grants Money to a User. 
The process is as follows:
- User requests a loan from the Client menu, inputting how much money they would like, 
and indicating some type of collateral
- The Banker can see all loans, and for each loan, choose to approve or reject it.
- Once approved, a Client can then withdraw Money from the loan Account.
- Each loan account also tracks the amount of money owed and how much money has
been paid. 
- The bank earns interest on the Account by compounding the amount of money owed in the account.
Right now, for a generic loan, the bank earns 17% apy on a loan.
- Clients can make a LoanPayment transaction, which reduces the amount of money owed, 
and increases the amount of money paid.

#### Collections and Data Persistence
One of the fundamental design concepts in this architecture are the CollectionManager classes.
The idea is that these classes provide a uniform interface for persisting collections to disk. 

Each CollectionManager implements the generic interface CollectionManager<T>, which includes the following methods:
- public void save(T obj) throws IOException;
- public T find(ID id) throws NoSuchElementException;
- public List<T> findByOwnerID(ID ownerId);
- public List<T> all();
- public void add(T element) throws IOException;
- public void clear();

There are five classes that implement this interface. They are:
- AccountsCollectionManager, responsible for handling all accounts.
- CredentialsCollectionManager, responsible for handling user login credentials
- InterestCollectionManager, which persists interest objects to disk, and attaches them to accounts
- TransactionsCollectionManager, which provides access methods for all transactions
- UsersCollectionManager, which contains each user.

The goal of each of these classes is to provide a facade of sorts that acts as
a liaison between the gui and the various backend classes. Thus, these classes provide a uniform
methodology for finding, updating, and saving data on the backend.

The actual data persistence is done by serializing objects to files in the data folder.
 
##### Other
- Right now, the loan application process requires a credit score, but we don't have a way of allowing
the User to input their credit score, or the backend look up their credit score. Instead, we have it hardcoded 
to 500.
- The logic for transferring money to/from a loan account is not fully flushed out.