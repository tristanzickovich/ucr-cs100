#Smart Pointers C++

##Intro (Are You Ready Kids?! Aye Aye Captain!)

Finally! <a href="http://spongebob.wikia.com/wiki/Patrick_Star" style="text-decoration:none" target="_blank">Patrick Star</a> finished his big programming project and it seems to work perfectly! <i>“I think it’s done, <a href="http://spongebob.wikia.com/wiki/SpongeBob_SquarePants" style="text-decoration:none" target="_blank">SpongeBob</a>! I’ve been working on it for like… 23 minutes already!”</i> Patrick exclaims. He’s tested every case he could possibly think of; all two of them that came to his head. Nothing could ruin his day now! He just made his deadline and is ready to go catch Jellyfish with his best friend! <i>“Ahh Patrick, it’s… beautiful! Perfectly formatted to look like <a href="http://spongebob.wikia.com/wiki/Gary_the_Snail" style="text-decoration:none" target="_blank">Gary</a>. A nice mixture of tabs and spaces. And no lines longer than 200 characters!”</i> says SpongeBob. As Patrick is about to submit his pride and joy, <a href="http://spongebob.wikia.com/wiki/Squidward_Tentacles" style="text-decoration:none" target="_blank">Squidward</a> pokes his head through the window and scoffs, <i>“don’t forget to check for memory leaks, idiot”</i>, quickly disappearing from sight. After all the time he’s put into it he wants to at least run valgrind and cppcheck to make sure it isn’t leaky…<br>
<i>“Let’s go catch those Jellyfish, Patrick!”</i> SpongeBob yells happily.<br>
<i>“Just a second SpongeBob, I gotta check one last thing”</i> Patrick says bluntly.<br>

Valgrind myBIGproject.out … tests … tests … tests … ENTER!<br>
<b>Leak Summary:</b><br>
<b>Definitely Lost: 150,486 bytes in 2 blocks!</b><br>

<i>“Aw Barnacles!”</i> Patrick cries. He was so close that he could taste victory.  Valgrind smashed his hopes into nothing better than the <a href="http://spongebob.wikia.com/wiki/Chum_Bucket" style="text-decoration:none" target="_blank">Chum Bucket</a>. Now he has to go back and manage all those pointers he used.  Had he only taken the time to learn smart pointers. Tsk tsk.

##Why Use Smart Pointers?
As Patrick learned, regular pointers (known as raw pointers) must be self-managed. They’re like <a href="http://spongebob.wikia.com/wiki/Sheldon_J._Plankton" style="text-decoration:none" target="_blank">Plankton</a>: they must be closely monitored or they have to potential to cause major problems. In other words, when you create a raw pointers, you must delete them yourself explicitly in the program. Forgetting to do so can cause major memory leaks! Deleting them yourself may not seem like a big deal, but you must correctly decide when to do so, so you don’t ruin the rest of your program. It’s also an easy thing to forget to do, which will cause you much hassle later on (like poor Patrick!).  
With smart pointers, the cleanup is done for you! That means you don’t have to determine where to place the delete! A huge benefit of this is if your program unexpectedly crashes before your program has a chance to deallocate memory, smart pointers will take care of it for you!
Each smart pointer has a unique set of instructions that determines when it will be deleted.  You will choose which of the smart pointers to use based on the circumstance. These aspects will be discussed under each individual smart pointer’s section.

##Before Getting Started
Smart pointers are a part of the Boost library.  To use them you must include the boost library in your code, or prepend `boost::` in front of your pointer declaration.  Specific examples will be shown with each pointer later.  Also, these pointers are a part of C++ 11. When compiling your code, you must include the C++ 11 flag:
If using UCR’s servers, you must enter the following command to enable the C++ 11 settings for the compiler:<br>
`source	     /opt/rh/devtoolset-2/enable`<br>
After this (or if using another system that supports compiling with C++ 11), when compiling use the following format:<br>
`g++ -std=c++11 yourfile.cpp`
	
##Should I Use Smart Pointers?
As a rule of thumb, smart pointers should be used when there is ownership of the object. To give an idea of ownership, say you have a program that has many functions.  You declare your pointer in one of them.  But two functions point to that same memory. Gasp! Which function actually owns it and is responsible for it? Ownership means a specific function “owns” the pointer and must delete it when appropriate. If you need another function to delete the pointer when you’re done using it, you should use raw pointers.  For more detail and examples on ownership, <a href="http://ericlavesson.blogspot.com/2013/03/c-ownership-semantics.html">click here!</a>


##Let’s Get Started!
Now that the basics have been covered, let’s get to the 
<a href="http://spongebob.wikia.com/wiki/Krabby_Patty" target="_blank">Krabby Patty</a> 
of this tutorial! In total, boost contains six different smart pointers.  These include 
<a href="http://www.boost.org/doc/libs/1_57_0/libs/smart_ptr/scoped_ptr.htm" target="_blank">scoped_ptr</a>,
<a href="http://www.boost.org/doc/libs/1_57_0/libs/smart_ptr/shared_ptr.htm" target="_blank">shared_ptr</a>, 
<a href="http://www.boost.org/doc/libs/1_57_0/libs/smart_ptr/scoped_array.htm" target="_blank">scoped_array</a>, 
<a href="http://www.boost.org/doc/libs/1_57_0/libs/smart_ptr/shared_array.htm" target="_blank">shared_array</a>, 
<a href="http://www.boost.org/doc/libs/1_57_0/libs/smart_ptr/weak_ptr.htm" target="_blank">weak_ptr</a>, 
and <a href="http://www.boost.org/doc/libs/1_57_0/libs/smart_ptr/intrusive_ptr.html" target="_blank">intrusive_ptr</a>. 
We will focus mainly on scoped and shared_ptr here.  Here we go!

##Scoped_ptr
###How its managed:
As inferred by the name, a scoped pointer will be deleted when it goes out of scope. Simply put, a scope can be viewed as everything in between at set of curly braces; for instance, everything inside a function has its own scope. Inside of a function (or class), any declared scoped pointers will be deleted when the function is destructed. 

###General Rules:
To maintain its proper functionality, there are a few rules that come along with the scoped pointer. 
<ol>
<li> They <b>CANNOT</b> be copied: if you try to set another pointer equal to your scoped pointer (myPointer = myScopedPointer), you will get an error.</li>
<li> They <b>CANNOT</b> be used inside containers: attempted use inside a container such as a vector will result in an error. If this is your intention, check out the shared pointers section.</li>
<li> They <b>CAN</b> be swapped with another scoped pointer: there is a built in swap function that allows you to swap scoped pointers.</li>
<li> They <b>CAN</b> be reset: There is also a built in reset function which allows you to reset the pointer to whatever you wish, deleting the existing pointer.</li>
</ol>
###How to Declare:

```
	#include <iostream>
	#include <boost/scoped_ptr.hpp>							//REF 1
	using namespace std;
		
	class PointerDemo{
		private:
			boost::scoped_ptr<int> num_pointer;    //REF 2
		public:
			PointerDemo()									//REF 3
			  : num_pointer(new int)
			{}
	};	
	
	int main(){
		boost::scoped_ptr<int> my_ints(new int);			//REF 4
		boost::scoped_ptr<int> my_swap_ptr(new int);
		my_ints.swap(my_swap_ptr);							//REF 5
		my_ints.reset(new int);								//REF 6
	}
```

The above code demonstrates declaration of scoped pointers. Use the references below for brief explanation:

<ol>
<li>REF 1: Make sure to inlcude the <code>boost library</code> for the scoped pointer
<li>REF 2: Scoped pointer declared as a class variable
<li>REF 3: Scoped pointer initialized in a constructor
<li>REF 4: Scoped pointer declared as a function variable
<li>REF 5: Built in <code>swap</code> function. It swaps the implicit and explicit parameters
<li>REF 6: Built in <code>reset</code> function. It resets the smart pointer to whatever you like.
</ol>

Quick Notes (see above for visual): 
<ul>
<li>Determine where you wish to declare your pointer. </li>
<li>Declare the scoped pointer as you would a vector, except with <code>boost::</code> prepended, and an argument after in parentheses.</li>
<li>The pointer type goes between the angle brackets. </li>
<li>The argument inside the parentheses: <code>new</code> and then the type of pointer. </li>
</ul>


TODO:

shared pointer tutorial

table of contents

code example
