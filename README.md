# ModalWX
A toolkit for designing Modal (kybd only) UIs for PCs 
using the wxWidgets cross-platform GUI library.
In a kybd only GUI, the kybd serves the dual functions of
text entry and screen-space selection.
Such a GUI is therefore neccesarily "modal"
in that it has modes of operation 
in which the meaning of keystrokes possibly changes.
The advantage of a kybd only GUI
is that it is simpler to design and simpler to use.
Engineering apps such as CAD tools
that involve a lot of interaction benefit most from kybd only UIs.
The modal UI toolkit consists of a ModeManager C++ structure
that interfaces with the windowing system.
In the case of wxWidgets, it interfaces with the wxWindow class.
And a few modes of operation designed for commun GUI functions
such as text input, message display and selection and file selection.
Modes are pushed onto and popped off the mode manager.
This file (modalwx.cpp) also contains a mode of operation
designed for editing the source code for a modal app.
At present it only implements source code navigation features.
We plan to add source code editing, codebase compilation and debugging features
to make this a full-blown IDE for writing cross-platform modal apps.
The approach we have taken in the design of this source code editor
is to target the narrow domain of modal source code.
By design, all of a modal app's source code
is contained in a single .cpp file.
This is unlike a typical codebase that has several different .cpp
files with associated .h files.
Instead of several small .cpp files that are navigated using a project directory browser,
we use a large single .cpp file that contains a large number of functions.  
The source editor mode implemented here is designed
to navigate this large set of C functions (this is a 5000+ line codefile).
We use 3 techniques to manage a codefile of this size.
1. We define 2 constructs above the C++ language level --
the Block and the Sub-Block.
Special demarcators inside comments are used to define blocks and sub-blocks.
2. We layout the source code in a 3 column format
with a wide center column that is used to work on the code
and 2 narrower columns to see what was before and after the code currently being worked on.
This makes is possible to view roughtly 150 lines of code at a time
which covers even the longest functions.
3. We use kybd based code folding (which we call summarization).
Blocks and sub-blocks can be summarized, functions and structures can be summarized.
Comment blocks can be summarized.
Summarization or de-summarization is done using Ctrl-S or Command-S.
It is very efficient and blends in well with code-editing and navigation
since there is no mouse involved in the operation.

By using these 3 techniques, 
it is possible to view this entire 5000 line codebase in a single page
then open a block or sub-block, open a function or structure inside it
and study the details of the implementation.
Then close that block, open another block and study it.
There is no other navigational mechanism needed.
The purpose of posting this codebase at this stage 
is to get people to use these codebase navigation techniques
and see if they are effective on understanding this 5000 line codebase. 

Building and Running the app:

The app is based on the wxWidgets cross-platform UI library.
To build it you first have to download and build the wxWidgets library.
We recommend using the current development version of wxWidgets which is 3.1.6.
You can get help for setting up wxWidgets on your PC from this forum:
https://forums.wxwidgets.org/viewforum.php?f=19&sid=0083f4684647607be2aef5bc34b48d82
The build process for the library depends on your platform:

OSX: 
We recommend building the library from source.
The simplest way is to download the source for wxWidgets.
Then open %wxWidgetsDir%/Samples/minimal/minimal_cocoa.xcodeproj
You should be able to build and run this sample.
It builds the wxWidgets library from source as part of its build process.
Then you can edit the project settings to replace minimal.cpp with modalwx.cpp.
This will build the modal app.

Windows:
We recommend Visual Studio 2022 Community edition as the IDE.
You download the source code.
Then you goto %wxWidgetsDir%/build/msw/
You open wx_vc17.sln in Visual Studio 2022
Build Debug and Release configurations (we recommend not dll but statically linked libraries).
This places the built libraries in %wxWidgetsDir%/lib/vc_x64_lib (or vc_lib).
Follow the instruction at https://forums.wxwidgets.org/viewtopic.php?p=196105#p196105
to create a new VS project and add modalwx.cpp to it.
This should build the modal app.

Linux:
We recommend using the CodeLite IDE which comes with wxWidgets pre-installed.
Create a new bare-bones wxWidgets based project and add modalwx.cpp to it.
This should build the modal app.

Running the app:
When you run the app,
it will ask you for the full path of modal.cpp.
When you enter this path,
it will load modal.cpp, parse it and display its blocks.
The arrow keys are for moving up and down the lines in the display.
PgUp and PgDn have their usual function.
To open a block you go to the block's line using the arrow keys
then you press Ctrl-S (Win, Linux) or Command-S (OSX)
To close a block, you go to the first line in the block an press Ctrl-S.
Any line that ends in {...} is summarized (closed) and can be opened using Ctrl-S 

Future Plans:
1. We plan to add source editing features to the source editor mode.
2. Then add the ability to compile a modal source code file and correct compilation errors.
This will be based on gcc (Win, Linux) and g++ on OSX.
3. Then add the ability to debug a modal app.
This will be based on gdb (Win, Linux) and lldb on OSX.
4. We plan to create modalWin32, a native version of the modal UI toolkit for Win32.
5. We plan to create modalX a native version of the modal UI toolkit for X-Windows on Linux.

Building your own wxWidgets based modal app:

modalwx.cpp serves as a source code toolkit for creating a wxWidgets based modal app.
The core parts of this toolkit are:
1. ModalWindow which derives from wxWindow,
and contains an SModeManager that manages the modes of a modal UI.
You instantiate a ModalWindow, instantiate a ModeManager and add it to the ModalWindow.
Then you push a starting mode onto the mode manager
that will be the mode of operation when the app launches and the ModalWindow is shown.
Over the course of the app's operation, you push and pop modes on the ModeManager.
2. SModeManager which is a c++ struct for managing the modes of operation of a modal UI.
SModeManager is initialized with the screen size and a font
that will be passed to all modes of operation.
It contains a stack of modes and methods to push and pop modes and get the current mode.
It also contains a method to serialize its current state to a file.
This enables the app to save the current state of the UI when it exits
and reload it when the app is relaunched.
ModalWindow calls modal_init in its constructor
which initializes the mode manager member variable of ModalWindow.
ModalWindow calls modal_exit in its destructor
which serializes the mode manager and then frees it.
3. SMode which is a C++ struct for representing the base properties of a mode of operation.
It contains a Union UModeExtension for representing mode specific extensions
to the base mode.
The Modal Toolkit provides a few extensions to the base mode for common UI operations.
These are:
a.   SModeMsg : a mode extension for displaying a user message
b.   SModeLineInp : a mode extension getting a line of user input 
c.   SModeSetSel : a mode extension for getting a choice from the user
from a set of selections.
d.   SModeFileSel : a mode extension for selecting a file from the file system
A modal app defines its own mode of operation 
which implements the desired UI function of that app.
For example modalwx.cpp defines SModeSrcEdr, a mode extension that is not part of the toolkit
but which implements the functionaly needed for brwosing code.

