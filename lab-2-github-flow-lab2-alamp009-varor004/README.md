# Lab 2: GitHub Flow
> Author(s): Andrew Lvovsky ([@borninla](https://github.com/borninla)), Brian Crites ([@brrcrites](https://github.com/brrcrites)), and Mike Izbicki ([@mikeizbicki](https://github.com/mikeizbicki)) and updated by Neftali Watkinson

# Part 1: Git

In the last lab you created three different versions of main.cpp to accommodate for the different cases of area calculator. In this lab we will continue our discussion of Git as a means of version control and how it, in combination with GitHub, can be used to efficiently collaborate with other developers so that different version of a system can be maintained in parallel. By the end of this section you should be familiar with

* how to create new branches
* how to switch between branches
* how to check your branch status
* how to view Git commit logs
* how to merge two branches together
* how to fix conflicts between branches
* important Git terminology

```
git clone --recursive <lab-clone-url>
```

> Note: whenever you close a repository with that contains submodules you will need to use the `--recursive` flag to receive the code within the submodule. If you forget to clone it recursively, you can run `git submodule update --init --recursive` to pull the submodules after the repo has been cloned. This can also be necessary when switching to a branch which has had a submodule added to it.



## Git Branch & Log

We mentioned in lab 1 that Git keeps track of the state of your code every time you make a commit. This commit is a snapshot of each of the files you've added and the changes since the last commit. By ordering these snapshots Git can keep track of how individual files change over time and allow you to move backwards and forwards in that file's history. Git can also keep track of mulitple parallel sets of changes to files, known as branches. This, combined with the ability to see how the combination of multiple changes together would affect a file is the primary way Git allows you to work collaboratively. 

Most software is written across a team(s) of developers and many developers might be working on the same file at the same time. If every time one of the developers made a commit the other developers had to download the updates to the files, fix any conflicts with what they were currently working on, and then test nothing was broken, development would slow to a crawl. Instead, each developer freezes the code at a specific commit (usually the most recent version), add or modify the code as necessary, and then they request all the other developers changes, fix conflicts, and test it before submitting their changes. 

The way Git represents the different developers working on the same code without constantly making collisions is with a feature called branching. Each branch represents a set of ordered changes to files which could be applied on top of a particular commit. 

Projects typically have many branches which represent development features, patches, or other changes. While most branches don't have any specific meaning as far as Git is concerned, it is typical to have a "main" branch which represents all the changes which have been reviewed, approved, and are ready to be deployed to a server with actual users. By default, GitHub will create a `main` branch ('master' for older repositories) for each project which serves this purpose, which is a common convention and the one you will use in this class. 

The visualization showing where each branch has split and been added to, as well as merged back into each other, is often known as the “working tree”. List the branches in your current project using the command:

```
$ git branch
```

This list should only have the single `main` branch created by Git or GitHub during initialization, such as when `git init` is run on the command line or you create a new repo on GitHub (which does this step as well, just behind the scenes).

Every time we develop a new feature or write a patch for your project you should create a branch specifically for it. In order to help keep track of which branches are for what features, and who is using them, we are going to use a standard naming convention to make things easier. Let's create a branch for adding user input to our project.

```
$ git branch <github-username>/add-user-input
```

All your branches will start with your GitHub username (which you should use in place of `<github-username>`) followed by a short dash separated description of the feature or patch you are working on (they should be dash separated as they will go into the GitHub url and urls don't allow underscores). Verify that our branch was created successfully with the following command:

```
$ git branch
```

You should see two branches now with an asterisk next to the `main` branch. This tells us that main is the currently active branch and if we commit any new changes they will be added to the main branch. Switch to the new branch using `git checkout` (below) and verify we have switched using `git branch`:

```
$ git checkout <github-username>/add-user-input
$ git branch
```

You should see that the asterisk is next to the `<github-username>/add-user-input` branch. Since the only thing you did was switch branches the working tree looks almost the same at this point. You should see a `main.cpp` in the repo you just cloned. Let's modify it so that it asks the user their name before saying hello:

```c++
#include <iostream>
#include <string>

int main()
{
    std::string name;
    std::cout << "What is your name?" << std::endl;
    std::cin >> name;
    std::cout << "Hello " << name << "!" << std::endl;

    return 1;
}
```

Since Git is already tracking the file (since it was added previously and is already in the repo) it can let you know that the file has changed and you might want to think about saving it in a commit. You can see the state of your repo, showing which files have been staged (added to a commit, but the commit not finalized), changed but not staged (modified but not added to a commit), or untracked (not currently part of the repo) using the following:

```
git status
```

You may find that you've modified a file but are unable to remember exactly what changes have been made since the last commit. Since Git is keeping track of all the file changes it provides a simple way to see what's been modified with the following command:

```
$ git diff main.cpp
```

Running this will show you the difference between the current version of `main.cpp` and the version at the last commit and you should see something like this:

```diff
 #include <iostream>
+#include <string>
 
 int main()
 {
-    std::cout << "hello git" << std::endl;
+    std::string name;
+    std::cout << "What is your name?" << std::endl;
+    std::cin >> name;
+    std::cout << "Hello " << name << "!" << std::endl;
+
     return 1;
 }
```

Here, the lines with a plus (`+`) at the beginning represent new lines and the lines with a minus (`-`) represent lines that were removed. We can now commit our changes to the current working branch the same way we committed them before:

```
$ git add main.cpp
$ git commit -m "Add user input”
```

Before this commit, the `<github-username>/add-user-input` and `main` branches were pointing to the same commit. When you run the command to create the commit, the `<github-username>/add-user-input` branch gets updated to point to this new commit. In Git parlance, since the `<github-username>/add-user-input` branch now has one more commit than the `main` branch, it is now 1 commit ahead of `main`.

Let's verify that our changes affected only the `<github-username>/add-user-input` branch and not the `main` branch. First, checkout the `main` branch, then cat the `main.cpp` file Finally, return to the `<github-username>/add-user-input` branch.

```
$ git checkout main
$ cat main.cpp
$ git checkout <github-username>/add-user-input
```

You should be able to see the difference between the files (your text editor may also ask you to load the new file since it has detected changes). Branches allow us to work on and track multiple features at the same time without having to worry about creating errors in our repository. 

We can also move forward and back in the changes that we’ve made to any branch, making it very easy to recover from mistakes. Looking at the raw differences in the code can make it difficult to see all the features and patches which have been applied. Luckily, Git provides a mechanism for viewing the commits that have been made through the Git logs. Run the following command and you should see your commit history:

```
$ git log
```

Each line starts with commit and then a series of numbers and letters known as a hash which are used to identify each commit. At the end of some of these lines you can see additional information like (`HEAD -> <github-username>/add-user-input`) and (`main`). These are indicators showing the last commits for various branches (or at least the last ones git knows about). It also has the commit message associated with that commit which, assuming you wrote good commit messages, should tell you what is in each commit.

> Tip: VS Code has an extension called "Git History" by Don Jayamanne that gives you a visual representation of the commit history. You can also commit, branch, pull, push, and many other things directly from VS Code.

When working on a feature you may also need to update the documentation in the README.md to reflect the changes. Proper documentation of your system will be part of your grade for this course. However, since the documentation for the labs is the instructions, you will only need to update your documentation for the projects.

While we were working on our `<github-username>/add-user-input branch` someone reported a bug in our `main` branch. In particular, the main function in our `main` branch returns 1 but a successful program should return 0. In UNIX, any return value other than 0 indicates that some sort of error occurred.

To fix this bug, we first checkout our `main` branch:

```
$ git checkout main
```

Then create a bugfix branch and check it out:

```
$ git branch <github-username>/main-return-hotfix
$ git checkout <github-username>/main-return-hotfix
```

Now we're ready to edit the code. Update the main function to return 0, then commit your changes:

```
$ git add main.cpp
$ git commit -m "Fix main to return 0 not 1”
```

## Git Merge & Conflicts

We want our users to get access to the fixed software and since we are using `main` as our "production" branch we need to add our hotfix code onto the `main` branch. This process is called "merging," which in this case is a simple procedure. Run the following commands to checkout the `main` branch and then merge in the changes from our hotfix branch:

```
$ git checkout main
$ git merge <github-username>/main-return-hotfix
```

This will try and automatically merge the hotfix code we wrote with the code in our current branch, which is `main` after the checkout command. Since the branch we are merging (`<github-username>/main-return-hotfix`) is the same as the branch we are merging onto (`main`) but with one more commit there should be no conflicts. This state is annotated in the Git status command as being "N commits ahead" of another branch. If there are changes to another branch which are not present in your current branch, Git will also show this is the status command as "N commits behind" (in both cases N is a non-zero number). Branches can be both ahead and behind each other at any given time, meaning new commits have been made to both branches without a merge happening. This is very common and when one branch is behind another and there are multiple commits which have modified the same file then we have what is known as a "merge conflict" since Git doesn't know what code from what commits to use.

> Note: Git will only tell you that there is a merge conflict with the same file has been changed by multiple commits and it doesn't know which segments of code are "correct". However, this does not mean that you can skip testing your code after a Git merge and assume that it is correct. Since code often has dependencies which span multiple files (a declaration of a function in one file and a definition in another) there could be changes to different files which do not conflict according to Git but nevertheless cause the compilation or functionality of your program to break

With real bugs on more complicated software, bug fixes won't be quite this easy. They might require editing several different files and many commits. It might take us weeks just to find out what's even causing the bug! By putting our changes in a separate branch, we make it easy to have someone fixing the bug while someone else is adding new features.

Our `<github-username>/add-user-input` feature is also ready now. We've tested it and are sure it's working correctly. It's time to merge this feature with the `main` branch. Run the commands:

```
$ git checkout main
$ git merge <github-username>/add-user-input
```

This one isn’t quite as easy as the last merge. We get an error message saying:

```
Auto-merging main.cpp
CONFLICT (content): Merge conflict in main.cpp
Automatic merge failed; fix conflicts and then commit the result.
```

This error is a merge conflict which we mentioned previously and is one of the hardest concepts for new Git users to understand. Let's dig more precisely into what caused this particular merge conflict. In our hotfix branch above, Git automatically merged the `main.cpp` file for us. It could do this because the `main.cpp` file in the `main` branch did not change after we created the bugfix branch (in other words the hotpatch branch was ahead but not behind `main`). Unfortunately, after we merged the hotfix branch into `main`, the `main.cpp` file changed. Now when Git tries to merge our changes from the `<github-username>/add-user-input` branch, it doesn't know which parts to keep from `<github-username>/add-user-input`, and which parts to keep from the changes we merged in from the hotfix branch. It is essentially impossible for Git to know what code to pick from which branch, so we have to fix it manually.

If you inspect the contents of the `main.cpp` file, you'll see something like:

```c++
#include <iostream>
#include <string>

int main()
{
<<<<<<< HEAD
    std::cout << "hello git!" << std::endl;
    Std::cout << “git is easy?” << std::endl;
    return 1;
=======
    std::string name;
    std::cout << "What is your name?" << std::endl;
    std::cin >> name;
    std:::cout << "Hello " << name << "!" << std::endl;

    return 0;
>>>>>>> <github-username>/add-user-input
}
```

As you can see, the file is divided into several sections. Any line not between the `<<<<<<<<` and `>>>>>>>>` lines is common to both versions of `main.cpp`. The lines between `<<<<<<<< HEAD` and `=======` belong only to the version in the current branch (`main`). And the lines between `=======` and `>>>>>>>> <github-username>/add-user-input` belong only to the `<github-username>/add-user-input` branch which is the branch you are merging in.

Sometimes you can pick code from each section to correct the issue, sometimes you can merge the two sections together, and other times it will take a more complex rewrite of the code to account for both changes. This totally depends on what has changed in the code between the two branches. In our case we only need to edit the lines between `<<<<<<<` and `>>>>>>>` to include only the correct information between the two branches. In our case, we want the return statement from the main branch, and all of the input/output from the `<github-username>/add-user-input` branch. So, we should modify the main.cpp file to be:

```c++
#include <iostream>
#include <string>

int main()
{
    std::string name;
    std::cout << "What is your name?" << std::endl;
    std::cin >> name;
    std::cout << "Hello " << name << "!" << std::endl;

    return 0;
}
```

After you've fixed the code to the above you should re-compile and re-test your code to make sure it functions as you expect. It should both take user input and return a 0 after it complete (you can use `echo $?` to print the return value of the last command executed to check the return value). Once we have resolved this merge conflict and tested that the new code works as expected, we can finalize our merge. We first tell Git that we've solved the conflict by adding the conflicting files, then we perform a standard commit. Since Git knows we are trying to merge a commit, it will actually generate a generic merging commit message for us.

```
$ git add main.cpp
$ git commit
```

As you can see, resolving merge conflicts can be a tedious process and many projects use various means to try to reduce the number of merge conflicts when possible. Some strategies you can use to try and reduce conflicts is to use good coding structure and splitting your classes into separate files each with its own header and source file and breaking up development tasks to reduce concurrent overlap. However, merge conflicts are often unavoidable especially when working with larger teams. You will have to deal with some type of code conflict resolution in industry, so you should practice this process now so you are prepared to deal with it later.


## Git Fetch

As we mentioned in the prior lab, you need to push you changes once you have committed your code so they can be uploaded to the repository. However, if your local copy is not up to date, you will need to do a pull. It is also possible to receive the remote changes (also known as upstream changes) without having git automatically attempt to merge them into your branch. For this you would use the following command, which will download the changes (but not try and merge them) for all your branches.

```
$ git fetch
```

After this, you can do a git merge to integrate the remote changes. `git pull` essentially runs a git fetch for a single branch and a git merge together in one step. You will be using `git push` and `git pull` extensively in your projects for this course, and merge conflicts will likely occur fairly regularly. Don't forget to push your changes.

# Part 2: GitHub

For most of your professional careers in industry, you will work on projects alongside other contributors. In order to reduce the friction that can occur between developers with different backgrounds, styles, and opinions, most companies, organizations, and projects dictate their preferred style and method of contribution. Dictating a preferred style gives your codebase a consistent look and makes it easier for developers to understand since things are done in a consistent manner. Having a preferred method of contribution, or workflow, makes sure that different contributors know who is working on what and enforce that their contribution meets the standards set out by the organization. Having a good workflow is an important step to make sure your organization (or for this course, your team) is working efficiently to create well-tested and high-quality code.

Many [git workflows](https://www.endpoint.com/blog/2014/05/02/git-workflows-that-work) exist today and they all have their pros and cons. However, GitHub flow is one of the simplest to learn and is our preferred method for this course. A brief overview of GitHub flow is found in the diagram below.

<img src="github-flow.png?raw=true" width="1000">

Source: [GitHub Guides](https://guides.github.com/introduction/flow/)

Since you now know the basics of how to use Git to track your code locally we will discuss how to use it in conjunction with GitHub to develop collaboratively. You and your partner will work on extending the Bash Unit Testing lab by adding a count function, along with unit tests for it. By the end of this section of the lab, you should be familiar with

* how to create issues to manage tasks
* how to use branches to reduce conflicts
* how to use pull requests to review and merge changes
* how to tackle merge conflicts
* how to tag releases
* how to revert to previous commits

## Issue Tracking

The first thing we are going to do is use the built-in GitHub issue system to track and manage tasks. Issues are useful to keep track of bugs that appear in projects as well as tracking work that still needs to be done. 

In GitHub each repository has its own associated set of issues, which are located under the `Issues` tab near the top of the repository. Currently, there aren't any open issues for this lab, so let's add some. Click on the green `New issue` button. On this page, you should notice some sections on the right. They are:

* Assignees - the developer assigned to the task, who will receive email alerts when comments are made on the issue. Keep in mind that more than one person can be assigned to the same issue, especially when they require some discussion.
* Labels - help to categorize the types of tasks that need to be completed. GitHub will autopopulate a list of tags for your repo, but you are free to edit or remove them as you see fit. We recommend using a basic set of `bug`, `test`, and `develop` tags to start with, and adding more as needed. GitHub tags can range from [relatively small and simple sets](https://medium.com/@dave_lunny/sane-github-labels-c5d2e6004b63) to [large hierarchies of color coded tags](https://robinpowered.com/blog/best-practice-system-for-organizing-and-tagging-github-issues/).
* Projects - which project this issue falls under. Projects are typically used to split the issues across teams or large features, and for this course we recommend you just have a single TODO project if you use it at all. If you are using the Projects feature, it is possible to have issues automatically added to a project's Kanban board. [You can read this link for more information](https://help.github.com/en/articles/configuring-automation-for-project-boards).
* Milestone - which milestone this issue falls under. This is similar to Projects, except milestones are usually associated with specific sprints and track completion of issues towards a particular feature or release.

Let's start by creating two issues for today's lab, with each partner creating one of the issues. Here we have listed the title is in bold followed by the text for the body of the issue. Note that you can use markup in the body of your issues which we encourage you to do.

* **Create a word counting function**: Add a function to `main.cpp` which counts the number of words input from the user
* **creating a word counting unit test**: Create unit tests for the word count function

Throughout the remainder of the lab, Partner 1 will be working on the count function and Partner 2 will be creating the unit test. Make sure to set the assignees according to who will be completing which portion. For creating a count function, choose the "enhancement" label. For the unit test, choose the "unit test" label. Don't worry about selecting Projects or Milestone for this lab.

> Note: If the "enhancement" or "unit test" label doesn't exist, click on "Edit labels" and then "New label" at the top right and add it.

## Unit Testing

Testing is a very important part of the software development process that is often overlooked. You will have a lab for Unit Testing in the future, but for this part we are going to get you jumpstarted with some basic unit tests.

Because C++ is a compiled language, it is fairly difficult to create unit tests for individual classes and functions because they need their own main for executing the test combined with the function or class being tested. Rather than try and invent our own testing paradigms/frameworks, we are going to use the fairly standard [Google Unit Test Framework](https://github.com/google/googletest) (gtest) for C++. While it's tempting to think we are using this because Google told us we needed more testing in the curriculum, it is actually because the author ([@brrcrites](https://github.com/brrcrites)) uses it in his research, and it has become the de-facto standard testing framework for C++ code.

Since we are going to write unit tests for this program we first want to break the project up into different modules. This is necessary in our case because currently we have our program implemented directly in main but the google test framework will require us to write a special main (covered below) used by them to create a test executable which we will run to test our code. Note that for this small example we will only be creating a single function as our module for testing, but the principles are the same if you have a single module or hundreds. Let's create our c-echo.h file as below:

```c++
#include <iostream>

std::string echo(int length, char** chars) {
    std::string ret = "";
    for(int i = 1; i < length; i++) {
        ret += chars[i];
        if(i < length - 1) {
            ret += " ";
        }
    }
    ret += "\n";
    return ret;
}
```

Notice that now instead of printing directly, we are generating a string which we will print to standard output in the main. The code has been slightly modified to allow it to return a string, for example it adds a `\n` character to the end rather than the `std::endl` it used before, but the functionality is the same. Now, let's create a `new` main2.cpp file:

```c++
#include "c-echo.h"

int main(int argv, char** argc) {
    std::cout << echo(argv, argc);
}
```

Run the following commands to allow git to track these files:
* `git add c-echo.h`    
* `git add main2.cpp`

One of the benefits of writing unit tests is that it forces you to think about how to subdivide a problem across a number of different classes and functions, because those become your testable units. The main itself cannot be unit tested since a different main will be needed to compile the unit tests. This is why in most large programming projects the only thing the main does is call a different function or create an object and call a method on that object.


## Git Submodules

We could download the gtest source code and include it in our git repository, but the gtest code is already has its own open source repository on GitHub. Instead of creating copies of the gtest framework everywhere with no easy way to keep track of version, git has a mechanism for including code from other git repositories in your own known as submodules. In order to include the gtest framework as a submodule, you'll first need to [find the clone link for the repository from their GitHub repository](https://github.com/google/googletest) and then use the `git submodule` command to add it as a submodule to the system.

```
$ git submodule add https://github.com/google/googletest.git
```

This will create a new googletest folder which contains all the code from the gtest repository. If you run `git status` you should also see that the googletest folder has already been added for committing, as well as a hidden .gitmodules file, which has the information for which submodules this repository should contain. 

> Note: when we add the googletest repository as a submodule it automatically downloads the source code to our local machine, but adds a link to the repository in GitHub. If you download a project containing a submodule from GitHub (which you will likely do at some point for your assignment) you will need to add a `--recursive` flag to your `git clone` command to pull the submodule along with the repository (`git clone --recursive <github-repo-url>`). If you forget to pull recursively and need to pull the submodule after cloning you can use the command `git submodule update --init --recursive` within the newly clone repository to pull any missing submodules.

Now we need to create our CMakeLists.txt file so it knows to compile the gtest code along with our own code by adding the following:

```
CMAKE_MINIMUM_REQUIRED(VERSION 3.0)

ADD_SUBDIRECTORY(googletest)

ADD_EXECUTABLE(c-echo
    main2.cpp
)

ADD_EXECUTABLE(test
    test.cpp
)

TARGET_LINK_LIBRARIES(test gtest)
TARGET_COMPILE_DEFINITIONS(test PRIVATE gtest_disable_pthreads=ON)
```

These changes do a few things for us. The first is the `ADD_SUBDIRECTORY` function, which makes CMake aware of the gtest framework. It will then look into that directory for another CMakeLists.txt file which will tell it how to compile that code and include it in our own. Next we have a `SET` function, which we use to set the C++ standard that we want to compile against to C++11. This is essentially equivalent to adding a `-std=c++11` flag to your g++ compilation. We also have a new `ADD_EXECUTABLE` line which requires a new test.cpp file. This test.cpp file is where we will write **all** our tests and create a main specifically for running those tests. This new executable will just run the tests and won't run the normal program functionality, so we still need the old executable to be generated. Finally, we add a `TARGET_LINK_LIBRARIES` function, which links our test program to the gtest library, making gtest a dependency for the test executable (note that the name *gtest* is actually defined by the Google Unit Test Framework, not by us). Finally, we have a `TARGET_COMPILE_DEFINITIONS` function, which adds a compilation definition to the build, which in this case disables googletest from looking for the pthreads library which hammer doesn't have. This is equivalent to adding a `-Dgtest_disable_pthreads=ON` flag which is a compiler pre-processor option. If you are doing this lab on your local machine,  you may be able to remove this last line of the CMakeLists.txt file.

## Writing a Unit Test

Now, let's create the test.cpp file and create our first unit test:

```c++
#include "c-echo.h"

#include "gtest/gtest.h"

TEST(EchoTest, HelloWorld) {
    char* test_val[3]; test_val[0] = "./c-echo"; test_val[1] = "hello"; test_val[2] = "world";
    EXPECT_EQ("hello world", echo(3,test_val));
}

int main(int argc, char **argv) {
  ::testing::InitGoogleTest(&argc, argv);
  return RUN_ALL_TESTS();
}
```

We start by including our c-echo.h so we have access to the `echo` function that we want to test, and we also `#include` the gtest framework. The gtest inclusion doesn't reference the gtest.h file from the directory directly, but instead uses a special gtest/ directory which we have access to through the `TARGET_LINK_LIBRARIES` function in the CMake (notice it matches the gtest from that command). 

After that we create our first unit test. There are lots of different types of tests that you can create using the gtest framework, and I suggest you read this [quick introduction to gtest guide](https://www.ibm.com/developerworks/aix/library/au-googletestingframework.html), and then reference this [gtest primer](https://github.com/google/googletest/blob/master/googletest/docs/primer.md) when you are looking for something more specific, in addition to the google test official documentation. The first test is defined with the `TEST` function, which takes a test set name (`EchoTest`) and a name for this specific test (`HelloWorld`). All tests with the same test set name will be grouped together in the output when the tests are run. In this test, we create a `char** test_val` with three values, which is the executable `./c-echo` followed by `hello world`. Remember that our function is programmed to skip the executable, so in order to test it properly we still need to pass the executable to the function. Finally, we create a new main which runs all the tests that we have written (this main is given in the documentation and you are unlikely to need to change it). 

Now that we've modified our CMakeLists.txt, we'll need to generate a new Makefile before we can compile the tests. Run the following commands to generate a new Makefile, compile the new targets, and then run the tests:

```
$ cmake .
$ make
$ ./test
```

When you run the tests, you should see an output like the following:

```
[==========] Running 1 test from 1 test case.
[----------] Global test environment set-up.
[----------] 1 test from EchoTest
[ RUN      ] EchoTest.HelloWorld
.../test.cpp:8: Failure
Expected equality of these values:
  "hello world"
  echo(3,test_val)
    Which is: "hello world\n"
[  FAILED  ] EchoTest.HelloWorld (0 ms)
[----------] 1 test from EchoTest (0 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test case ran. (0 ms total)
[  PASSED  ] 0 tests.
[  FAILED  ] 1 test, listed below:
[  FAILED  ] EchoTest.HelloWorld

 1 FAILED TEST
```

Oops, we failed our first test. Lets take a look at the output and try and see why.

```
Expected equality of these values:
  "hello world"
  echo(3,test_val)
    Which is: "hello world\n"
```

The problem is that we expected `hello world` to be returned, but we forgot that the function actually adds a newline to the end of the string so the prompt will go to the next line. At this point we have two options (1) if we actually want the function to return `hello world`, we need to modify the function or (2) if the function should actually return a newline then we need to change the test. In a test driven design methodology, we would actually write one or a small number of basic unit tests, then develop a small part of our system until we pass those unit tests, and then repeat that process until we've finish our function (and then we already have our unit tests). Here, the function echo should probably directly mimic the input so we don't actually want that newline in the function but instead in the main. Go ahead and modify the function in c-echo.h so it no longer returns the newline and instead add that newline to the main2.cpp (this way we still get easy to read output without it affecting our function). Re-run the test to make sure you are now passing (since the tests don't run the other main, the added newline there won't be a problem for testing), you should see something like this:

```
[==========] Running 1 test from 1 test case.
[----------] Global test environment set-up.
[----------] 1 test from EchoTest
[ RUN      ] EchoTest.HelloWorld
[       OK ] EchoTest.HelloWorld (0 ms)
[----------] 1 test from EchoTest (0 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test case ran. (0 ms total)
[  PASSED  ] 1 test.
```

> Make a commit here with the CMakeLists.txt, main2.cpp, test.cpp, and c-echo.h file as well as the googletest and .gitmodules files

## The count function

Now that you had your primer with tests, let us start creating branches. First, we are adding `c-count.h`, we should also update our `CMakeLists.txt` file in the `ADD_EXECUTABLE` field like so:

```
CMAKE_MINIMUM_REQUIRED(VERSION 3.0)

ADD_SUBDIRECTORY(googletest)

ADD_EXECUTABLE(c-echo
    main2.cpp
)

ADD_EXECUTABLE(c-echo-count
    main.cpp
)

ADD_EXECUTABLE(test
    test.cpp
)

TARGET_LINK_LIBRARIES(test gtest)
TARGET_COMPILE_DEFINITIONS(test PRIVATE gtest_disable_pthreads=ON)
```

Let's also change our .gitignore to reflect our additions:

```
c-echo-count
test

CMakeCache.txt
CMakeFiles/
cmake_install.cmake
Makefile
```

Go ahead and git add, commit, and push these files. Make sure the partner that didn't add the files does a `git pull` so that everyone is up-to-date going into the next section.

> Note: you will be asked to authenticate every push you make to GitHub with your password. If you would like to avoid this you can use the git credential cache to temporarily store your password. With this system you set a timeout (we recommend 1 hours = 3600) and only need to re-enter your password after the timeout period has elapsed. You can see [an example of how to enable the git credential cache here](https://git-scm.com/docs/git-credential-cache#_examples).

## The Importance of Effective Branching

In the Git lab, you learned that branching is used when developers want to change code without having to deal with conflicts between other contributors. When working on a team, it is wise to separate what each person is working on (and often each feature that person is working on) by making a branch unique to that change or set of changes. When that team member finishes their part, they can then merge back into the main development branch to signify that it is complete and should be part of the code everyone uses moving forward.

> Note: You should refer back to the links we provided in the git lab for creating good commit messages, however these are not the only good methods for commit messages. Lots of organizations have requirements on commit messages as they do reviews and style. Some of these try and categorize the commits such as the [gitmoji](https://gitmoji.carloscuesta.me/) system.

Each partner should now create a branch from the main branch. Both partners should title their branches as such, respectively:

```
$ git branch <partner-1-github-username>/count-func
$ git branch <partner-2-github-username>/count-test
```

You should prepend your branch with your username to make it easy to see who owns which branch and to avoid naming conflicts between individual contributors' branches. After you have each created your individual branch, **it is very important** that you change to that branch with `git checkout <branch-name>`

> Tip: If you are even in doubt, you can use `git branch` or `git status` to determine which (if any) branch you are currently on.

### The First Branch (Partner 1)

Partner 1 should add the following count function to the provided `c-count.h`:

```c++
#include <iostream>

unsigned int count(const std::string& str) {
    unsigned int counter = 0;
    bool on_space = true;
    
    for(int i = 0; i < str.size(); i++) {
        if (std::isspace(str[i]))
            on_space = true;
        else if (on_space) {
            counter++;
            on_space = false;
        }
    }

    return counter;
}
```

This function counts the number of words in a string, delimited by whitespace. It takes a string and iterates through every character. When whitespace is encountered, a flag gets set. If the next character doesn't contain whitespace, the counter is incremented and the flag is unset.

### The Second Branch (Partner 2)

Partner 2 should add or update the following unit tests to `test.cpp`, taking special note to update the tests from the previous lab since we have updated the name of the executable that is generated by CMake:

```c++
#include "c-echo.h"
#include "c-count.h"

#include "gtest/gtest.h"

TEST(EchoTest, HelloWorld) {
    char* test_val[3]; test_val[0] = "./c-echo-count"; test_val[1] = "hello"; test_val[2] = "world";
    EXPECT_EQ("hello world", echo(3,test_val));
}

TEST(EchoTest, EmptyString) {
    char* test_val[1]; test_val[0] = "./c-echo-count";
    EXPECT_EQ("", echo(1, test_val));
}

TEST(CountTest, HelloWorld) {
    std::string test_str = "hello world";
    EXPECT_EQ(2, count(test_str));
}

TEST(CountTest, EmptyString) {
    std::string test_str = "";
    EXPECT_EQ(0, count(test_str));
}

TEST(CountTest, ManySpaces) {
    std::string test_str = "   this   string has     weird   spacing";
    EXPECT_EQ(5, count(test_str));
}

int main(int argc, char **argv) {
    ::testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```

One more thing, Partner 2. Your `c-count.h` should still look like below:

```c++
#include <iostream>

// count function should go here
```

Let's say you didn't know your partner was editing the same file in another branch. To test the functionality of your unit test, you should write a count function that always returns 0.

```c++
#include <iostream>

int count(const std::string& phrase) {
    return 0;
}
```

Partner 2 should now build and compile using `cmake .`, `make`, then run `./test`. You should see 2 tests fail, CountTest's HelloWorld and ManySpaces. EmptyString passes since we are returning 0 at all times.

### The Push

Both partners should now commit and push these changes to their branches (if you haven't already).

> Note: Since these branches were made locally and need to be pushed up to GitHub, you will be prompted to run `git push --set-upstream origin <branch name>` when pushing a branch for the first time.

Each partner should also refresh their local git's view of what branches exist and what they have by running `git fetch`. This will sync your git's local view of the world with what is in GitHub showing new changes. Rather than merging your branches into main through the command line, it is good practice to open a pull request that can be reviewed by your team members. 

## Pull Requests

Most organizations lock their main development branches so that code reviews and a passing set of unit and/or integration tests are required before code can be merged into the main development branches. The GitHub pull request system allows for maintainers of repositories to block changes from being added to certain branches until they meet specific requirements, such as having a minimum number of approved code reviews and successfully passing automated tests. By having GitHub enforce these rules for us *before* our stable branches (like main) can have code added to them, we greatly increase the changes that our stable branches will continue to be stable.

Let's try doing this now. Both partners should open up their GitHub repository to prepare the pull request. 

### Creating a Pull Request

Partner 1 should press on the "Branch" dropdown menu located right above the repository files list and select `<partner-1-github-username>/count-func`. This will cause you to switch to viewing the new branch, at which point you should click on the "New pull request" button right next to the "Branch" dropdown. This will bring up the editing screen for the pull request. You might notice that this page looks similar to creating a new issue, with an added "Reviewers" section. Partner 1 should add a reviewer by clicking on the "Reviewers" section, searching for Partner 2's username, and selecting them. This will assign Partner 2 to review the pull request and will add them to the notification list so they will receive emails about any comments or discussion made on the pull request. Make sure to also assign yourself (Partner 1) under the Assignees section and give it a label that best suits the addition. Title it `Add Count Function` and leave a brief description of what is being added. In most cases, a description should be well-detailed so the reviewer can easily understand what modifications they need to look for. Once done finalizing the pull request, click on "Create pull request".

Partner 2 should follow the same steps above, except by selecting `<partner-2-github-username>/count-test` from the branch dropdown menu. Also, title it `Count Unit Test`. Make sure to choose Partner 1 as the reviewer and Partner 2 as the assignee.

### Issue Tracking Strikes Back!

Previously, in order to close an issue, we went to the issues page and closed it by hand. However, this is not the typical method for closing issues on GitHub. Instead, issues are usually closed through special annotations in commit messages. This has the benefit of not only closing the issue when the commit with the annotation is merged into main automatically, but also linking the pull request or commit that has the annotation to the issue automatically so it can be easily referenced later if there is an issue.

Both partners should amend their latest commit message using the `--amend` flag, which re-opens the last commit allowing it to be changed. Type the following to do so:

```bash
git commit --amend
```

This will open up your editor. The very top should contain your most recent commit message. Go ahead and change the message to `Fixes #<issue-number>`.

> Note: the issue number can be found to the right of your issue's title (after the hashtag). Don't forget to include the hash before the issue number.

Save the message and exit the editor, then push once more. You will need to force this push by entering `git push -f`, since this commit was already pushed earlier and needs to be updated.

Go to your pull requests on GitHub and you'll notice that every commit made to a branch that has an open pull request will be listed. You should also notice that your commit message has a hyperlink. If you click on the number, it should send you to the specified issue. Once the pull request is merged and closed, GitHub will take care of closing the issue for you automatically. Nice!

### Reviewing a Pull Request / Code Reviews

When developing on existing projects, team members will usually be working on integrating features, squashing bugs, refactoring, or otherwise working with existing code. To help make sure that new changes will not adversely affect the system and are of generally high quality, code reviews are usually held with other members of the team. While there are lots of different ways to perform a code review, it is typically done through comments (either generally on the pull request or related to specific lines of code changed in that pull request). This is because GitHub is primarily focused on remote and decentralized teams. Because you are likely to see your team members often, you may find it easier to work through code reviews in person. This is fine, but we do request that you write a simple "Reviewed in person" comment before merging a pull request (more on merging in a later section).

You should review this [guide](https://smartbear.com/learn/code-review/best-practices-for-peer-code-review/) for some good practices for code reviews, but here are some general rules to keep in mind:

* Start with a positive note: reviewees will be more receptive to your comments if you start with something positive rather than only pointing out the negatives.
* Reference the code not the coder: when you make a comment, reference the code and not the person who wrote the code.
* Use line item suggestions: when reviewing the files, you can make a comment on a specific line making it much easier for the reviewee to know exactly what code you are referencing with your comment
* Don't assume you know better: you may think a piece of code is incorrect or inefficient but it maybe that you simply don't have the full context of the problem. Rather than saying something is wrong suggest a way it might be improved and ask if there is an issue with your proposed solution or request more context about why a piece of code was written the way it was.

Partner 2 should now review Partner 1's pull request. Navigate to the "Pull requests" tab and choose the `Add Count Function` pull request. From here, you can review comments made about the pull request under the "Conversation" tab, all commits associated with that pull request under the "Commits" tab, and what changes have been made to various files under the "Files changed" tab.

Near the bottom of the "Conversation" tab, you should see a green checkmark next to a title reading "This branch has no conflicts with the base branch". This means GitHub found no merge conflicts between this branch and the main branch. Go ahead and press "Merge pull request", and it will ask you to confirm the merge which you should do. After you have merged it will ask if you want to delete the branch which you should do as it keeps your list of actively used branches small and clean. Note that this will remove the branch on the remote but you will need to run `git branch -d <branch-name>` to remove the local branch (replacing `<branch-name>` with the name of the branch you want to remove).  This will cause GitHub to essentially call `git checkout main && git merge <partner-1-github-username>/count-func` behind the scenes and merge the branch into main. You are now given the option to delete the branch, which you should do. This keeps the number of branches associated with your repo manageable and stops you from accidentally re-using a branch for a feature it wasn't named for.

> Note: deleting the branch will not automatically remove it from your local git (remember git doesn't automatically get updates from GitHub). You should switch off the branch on your local git (usually by checking out the main branch) and then run `git fetch` to sync your local git with GitHub, which will then trim the branch from your local git. If you do not do this and try and push from the deleted branch you will receive an error.

Partner 1 should navigate to the "Pull requests" tab and select the `Count Unit Test` pull request. Unlike the first pull request, this one has a merge conflict and GitHub cannot merge it automatically.

#### Fixing a Merge Conflict

Let's resolve the merge conflict. Partner 1 should type the following commands which will change their current branch to main, update it to match what is on GitHub, and switch them back to their new branch:

> Note: You might get an error about your `test` executable being overwritten during the checkout process. If this happens, simply remove the executable by typing `rm test`, then try again.

```bash
git checkout main
git pull
git checkout <partner-2-github-username>/count-test
git merge main
```

The last line will attempt to auto-merge main with the current branch, which we already know from GitHub will cause a conflict. You should see something like below:

```
Auto-merging c-count.h
CONFLICT (content): Merge conflict in c.count.h
Automatic merge failed; fix conflicts and then commit the result.
```
If you open `c-count.h` in a text editor, you should see some lines that include `<<<<<<< HEAD`, `=======`, and `>>>>>>> main`. 

```c++
#include <iostream>

<<<<<<< HEAD
int count(const std::string& phrase) {
        return 0;
=======
unsigned int count(const std::string& str) {
        unsigned int counter = 0;
        bool on_space = true;

        for(int i = 0; i < str.size(); i++) {
                if (std::isspace(str[i]))
                        on_space = true;
                else if (on_space) {
                        counter++;
                        on_space = false;
                }
        }

        return counter;
>>>>>>> main
}
```

Everything past `<<<<<<< HEAD` and before `=======` is the new code coming in from the branch being merged, while everything past `=======` and before `>>>>>>> main` is existing code from the main branch (the branch that is being merged into). We'd like to accept the fully-built count function, so go ahead and delete everything past `<<<<<<< HEAD` and before `=======`. Make sure to also delete these indicators, or you will get compilation errors.

> Note: while this merge was relatively straight-forward and we just wanted to accept what was in main, there are times when you will need to merge both branches in order to get what you actually want. This may lead to additional errors, so it's always important to test the code to make sure it's still working after a merge has been resolved.

Once you are done, be sure to `git add` the file you just modified, commit it, and push to GitHub. When committing a merge, simply typing `git commit` without the `-m` flag will automatically populate a merge message. Partner 1 should now refresh the `Count Unit Test` pull request on GitHub. Notice that every commit made in the branch should appear within the pull request automatically, so you don't need to close and re-open a pull request if there are issues. pull requests and the conversation and commit history associated with them can serve as valuable documentation about why certain design or coding decisions were made.

Using the good practices for code reviews reference above, make sure everything looks good with this pull request. There should no longer be any merge conflicts, so go ahead and press "Merge pull request" and repeat the same process of deleting the branch as stated above.

## Reverting Commits

Throughout the development process, there are times where new commits may introduce problems in existing code. For these situations we can use git to revert the changes so our main is functional while we debug the issue on our own branch. This is very important for larger teams who might have contributors branching off of a broken main, leading to frustration when a developer thinks their changes are broken when really it was main that was broken.

> Tip: If you discover a *regression* as you are testing your changes that does not seem obviously related to your changes, you may want to quickly checkout main and test to see if the bug exists there, too.

> Advanced tip: When tracking down a regression, it helps to identify the particular commit that introduced it.  To do this, first identify an older commit when the test case is known to work.  The offending commit can now be located using *binary search*.  The ability to isolate bugs in this way is a very powerful feature of version control.  There is a tool (`git bisect`) that automates this process for you.

Uh-oh, we forgot about adding the function to `main.cpp`! Let's do that now. Since Partner 1 resolved the merge conflict, Partner 2 should do this part.

```c++
#include "c-echo.h"

int main(int argv, char** argc) {
    std::cout << "Count of output: " << count(echo(argv,argc)) << std::endl;
}
```

You should be in the main branch, so go ahead and git add, commit, and push this change. Now try building using `cmake .` and `make`. Oops, looks like something broke! It seems that we forgot to add `#include "c-count.h"`.

Let's fix this using reversion! To revert back to the last commit, type the following command.

```
git revert HEAD
```

This will create a new commit that is left off at the last commit. Let's add the main function once more, this time not forgetting `#include "c-count.h"`.

```c++
#include "c-echo.h"
#include "c-count.h"

int main(int argv, char** argc) {
    std::cout << "Count of output: " << count(echo(argv,argc)) << std::endl;
}
```

Please git add, commit, and push one more time.

> Note: To revert to two or more commits down the chain, replace `HEAD` with the commit reference (the long string of characters ("hash") that identify each commit that can be viewed using `git log`).  When using a hash, you actually only need to specify the first several characters of the hash.  You can also use `HEAD^` to refer to the commit before the last one; `HEAD^^^` refers to the fourth-to-last commit.

## Tagging

Often, you’ll want to tag certain git commits because they are special. The most common reason to tag a commit in git is to tag it as a stable version, especially when using the [semantic versioning schemes](https://semver.org/). Let's assume that our code is now ready for release as `v1.0.0` and tag it as such.

```
$ git tag -a v1.0.0 -m “Initial stable release”
```

The `-m` in this case works the same as it does when performing a commit, allowing us to add a message but bypassing the editor to do so. The `-m` flag will only allow you to set the title, so make sure to follow the 50-character commit title rule from the Git lab and only use it when no additional information would be necessary in the commit message body. If you wanted to tag a commit besides the current commit you would need to add the commit hash after the version number (but before the message). Normally a 1.0.0 release would have lots of additional notes on the current state of the software, but this is just an example. These tagged versions are stored in a special section of the repository on GitHub along with compressed versions of the source files to make public releases easier. This command creates the tag, but like most git commands it will only create the tag locally. In order to push this tag to GitHub you need to execute the following command.

```
git push origin v1.0.0
```

This will push the tag we just created (`v1.0.0`) to GitHub which is our remote git repository. The remote repository (by convention) is named `origin` which is where we designated the tag to be pushed to.

> Note: It is possible to create both annotated tags using the `-a` flag and unannotated flags. Annotated flags carry additional information about their creation, so you **must** use them when adding a tag for your assignment.
