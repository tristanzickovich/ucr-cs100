#Smart Pointers C++

##Overview
This tutorial covers smart pointers from the 
[boost library](http://www.boost.org/doc/libs/1_57_0/libs/smart_ptr/smart_ptr.htm).
Smart pointers help with memory managment, helping prevent memory leaks.
They're self-managed, automatically deleting themselves when they're no longer needed.
In this tutorial we will focus on `scoped_ptr` and `shared_ptr`.
We'll cover *why*, *where*, and *how* to use them.

##Intro (Are You Ready Kids?! Aye Aye Captain!)
Finally! [Patrick Star](http://spongebob.wikia.com/wiki/Patrick_Star) finished his big programming project and it seems to work perfectly! 
*“I think it’s done, [SpongeBob](http://spongebob.wikia.com/wiki/SpongeBob_SquarePants)! 
I’ve been working on it for like… 23 minutes already!”* Patrick exclaims. 
He’s tested every case he could possibly think of; all two of them that came to his head. 
Nothing could ruin his day now! He just made his deadline and is ready to go catch Jellyfish with his best friend! 
As he's about to submit his pride and joy, [Squidward](http://spongebob.wikia.com/wiki/Squidward_Tentacles) pokes his head through the window and scoffs, *“don’t forget to check for memory leaks, idiot”*, quickly disappearing from sight. 
After all the time he’s put into it he wants to at least run valgrind and cppcheck to make sure it isn’t leaky…<br>
*“Let’s go catch those Jellyfish, Patrick!”* SpongeBob yells happily.   
*“Just a second SpongeBob, I gotta check one last thing”* Patrick says bluntly.   

valgrind myBIGproject.out … tests … tests … tests … ENTER!   
**Leak Summary:**   
**Definitely Lost: 150,486 bytes in 2 blocks!**   

*“Aw Barnacles!”* Patrick cries.
He was so close that he could taste victory.
Valgrind smashed his hopes into nothing better than the [Chum Bucket](http://spongebob.wikia.com/wiki/Chum_Bucket).
Now he has to go back and manage all those pointers he used.
Had he only taken the time to learn smart pointers. Tsk tsk.

##Why Use Smart Pointers?
As Patrick learned, regular pointers (raw pointers) must be self-managed. 
They’re like [Plankton](http://spongebob.wikia.com/wiki/Sheldon_J._Plankton): must be closely monitored or can cause major problems.
In other words, when you create raw pointers, you must delete them yourself explicitly in the program. 
Forgetting to do so can cause major memory leaks! 
Deleting them yourself may not seem like a big deal, but doing so incorrectly can ruin the rest of your program. 
With smart pointers, the cleanup is done for you, meaning you don’t have to delete them! 
Each smart pointer has a unique set of instructions that determine when it will be deleted.  
They're easy to declare, leaving no reason not to use them where applicable. Let's take a look at *scoped* and *shared pointer* declarations:

```
	#include <iostream>
	  //Make sure to include the boost library for the scoped and shared pointer
	#include <boost/scoped_ptr.hpp>
	#include <boost/shared_ptr.hpp>
	using namespace std;
		
	class MrKrabbsDebt{
		private:
			  //Scoped and Shared pointers declared as (private) class variables
			boost::scoped_ptr<int> AmountOwedFriend;
			boost::shared_ptr<int> AmountOwedCompany;
		public:
			  //Scoped and Shared pointers initialized in constructors
			MrKrabbsDebt()
			  : AmountOwedFriend(new int)
			{}
			MrKrabbsDebt(boost::shared_ptr<int> num_ptr)
			  : AmountOwedCompany(new int)
			{}
	};	
	
	int main(){
		  //Scoped pointer declared as a function variable
		boost::scoped_ptr<int> OweSpongebob(new int);	
		boost::scoped_ptr<int> OwePatrick(new int);
		boost::scoped_ptr<int> OwePlankton(new int);
		  //Built in swap function. It swaps the implicit (OweSpongebob) with the explicit (OwePatrick) parameter
		OweSpongebob.swap(OwePatrick);	
		  //Built in reset function. It resets the smart pointer to a new pointer of the same data type
		OwePlankton.reset(new int);		
		OwePatrick.reset(new int);
		
		  //Shared pointer declared as a function variable
		boost::shared_ptr<int> OweKrustyKrab(new int);
		  //Vector of shared pointers declaration
		typedef vector< boost::shared_ptr<int> > OweChumBucket;
		  //Shared pointer passed as the explicit parameter to an object constructor
		MrKrabbsDebt one(OweKrustyKrab);
		MrKrabbsDebt two(OweKrustyKrab);
		MrKrabbsDebt three(OweKrustyKrab);
	}
```
##Before Getting Started
Smart pointers are a part of the boost library.
In total, boost contains six different smart pointers.  
These include 
[scoped_ptr](http://www.boost.org/doc/libs/1_57_0/libs/smart_ptr/scoped_ptr.htm),
[shared_ptr](http://www.boost.org/doc/libs/1_57_0/libs/smart_ptr/shared_ptr.htm), 
[scoped_array](http://www.boost.org/doc/libs/1_57_0/libs/smart_ptr/scoped_array.htm), 
[shared_array](http://www.boost.org/doc/libs/1_57_0/libs/smart_ptr/shared_array.htm), 
[weak_ptr](http://www.boost.org/doc/libs/1_57_0/libs/smart_ptr/weak_ptr.htm), 
and [intrusive_ptr](http://www.boost.org/doc/libs/1_57_0/libs/smart_ptr/intrusive_ptr.html). 
We will focus mainly on scoped and shared pointers here.   
To use them you must include the boost library in your code, and prepend `boost::` in front of your pointer declaration, as shown above.
Being part of C++ 11, when compiling your code you must include the C++ 11 flag.  
If using UCR’s servers, you must enter the following command to enable the C++ 11 settings for the compiler:   
`source	     /opt/rh/devtoolset-2/enable`   
You only need to enter this once and it will work for the remainder of your login session.   
After this (or if using another system that supports compiling with C++ 11), compile using the following format:   
`g++ -std=c++11 yourfile.cpp`

##When To Use Smart Pointers
As a rule of thumb, smart pointers should be used when there is ownership involved.
To give an idea of ownership, say you have a program that has many functions.
You declare your pointer in one of them, but two functions use a pointer to that same memory location.
Gasp! Which function actually owns it and is responsible for it?
Ownership means a specific function “owns” the pointer and must delete it when appropriate.
If there is no instance of ownership, you should use raw pointers instead.  
For more detail and examples of ownership, 
[click here!](http://ericlavesson.blogspot.com/2013/03/c-ownership-semantics.html)

##Scoped_ptr
###How it's managed:
As inferred by the name, a scoped pointer will be deleted when it goes out of scope. 
Simply put, a scope can be viewed as everything in between at set of curly braces.
For instance, everything inside a function has its own scope. 
Inside of a function (or class), any declared scoped pointers will be deleted when the function is destructed. 
What sets it apart from normal variables declared in a scope is it is non-copyable. 

###General Rules:
To maintain its proper functionality, there are a few rules that come along with the scoped pointer. 

1. They **CANNOT** be copied: if you try to set another pointer equal to your scoped pointer (myPointer = myScopedPointer), you will get an error.
2. They **CANNOT** be used inside containers: attempted use inside a container such as a vector will result in an error. If this is your intention, check out the shared pointers section.
3. They **CAN** be swapped with another scoped pointer: there is a built in swap function that allows you to swap scoped pointers.
4. They **CAN** be reset: There is also a built in reset function which allows you to reset the pointer to another smart pointer of the same data type, deleting the existing pointer.

##Shared Pointer
###How It's Managed
Unlike the scoped pointer, the shared pointer is not deleted when an instance of the pointer goes out of scope.  
Why? Say you have a class which contains a pointer as a member variable.
In another function you create several objects of this class, all of which need access to that pointer.
If it were to get deleted when one of the objects goes out of scope, your other objects would have no pointer to use, causing a problem!
The shared pointer will be deleted when there are no remaining objects that own it (when the last object owning the pointer is destroyed).

###General Rules:
There are a few rules that go along with the shared pointer. 

1. They **CAN** be copied: you may set another shared pointer equal to your shared pointer.
2. They **CAN** be used inside containers: they may be used inside a container such as a vector.

###Quick Notes For Using Either Pointer: 

* Determine where you wish to declare your pointer.
* Declare the shared pointer as you would a vector, except with `boost::` prepended, and an argument after in parentheses.
* The pointer type goes between the angle brackets.
* The argument inside the parentheses: `new` and then the type of pointer.

###Other Smart Pointers (A Brief Coverage)
**scoped_array and shared_array**   
The *scoped_array* and *shared_array* follow most of the same conventions as the *scoped_ptr* and *shared_pointer* respectively. 
You use the array version instead of the pointer version if you wish to have an array of the pointer.
They are used and declared the same way, and have the same member functions.
The main difference comes during destruction. 
The destructor accounts for the pointer being an array, correctly deallocating memory (it calls `delete [] smart_array` rather than `delete smart_array` as the pointer version would).   
For more info, [click here for *scoped_array*](http://flylib.com/books/en/1.437.1.24/1/)
or [click here for *shared_array*](http://flylib.com/books/en/1.437.1.26/1/).

**weak_ptr**   
A *weak pointer* is *only* used alongside *shared pointers*, providing mutual ownership with the *shared pointer*.
It provides one key additional member function called `lock()`.
A *weak pointer* calling `lock()` will return a *shared pointer* (the *shared pointer* returned will be empty if no *shared pointer* objects remain).
`lock()` is used to prevent accidental deletion of an object.   
For more info, [click here!](http://www.drdobbs.com/weak-pointers/184402026)

**intrusive_ptr**   
The *intrusive pointer* acts quite similarly to a *shared pointer*, with one key difference.
It directly calls a reference counter, which keeps track of the number of *intrusive pointers* in use.
When intrusive pointers are freed, if the reference count drops to zero, the object it points to will be destroyed.
It is mainly used to increase performance of a program.   
For more info, [click here!](http://baptiste-wicht.com/posts/2011/11/boost-intrusive_ptr.html)
