# ModalWX
(reading time ~ 3 min)  
wxWidgets is a cross-platform GUI toolkit for building WIMP style GUIs.  
Such GUIs divide the screen spatially into Windows.  
The smallest windows are controls such as clickable Buttons   
which serve as the units of the GUI.  
The mouse is used to select space on-screen,   
making it easy to navigate this spatial organization of the screen.  
WIMP UIs are non-modal even though they may contain modal elements such as dialogs.  

ModalWX extends wxWidgets with the ability to create "Modal" GUIs.  

Modal GUIs do not layout the screen spatially using windows.  
Instead they layout the app's interaction time temporally using "modes of operation".  
Each mode of operation paints to the entire screen   
and has exclusive control over user input while it has focus.  
A mode defines a set a "user intents",    
which is the user's expression of their intent to do something   
expressed as a specific input gesture.  
A mode implements an intent handler for each intent it defines.  
This intent handler performs the action the user wants and updates the screen.  
An intent (and it's handler) is the unit of the UI in a Modal UI,  
much like a control is in a WIMP GUI.  
There is one primary mode that a Modal app launches with.  
Subsequently, over the app's lifetime, different modes may pop-up   
in front of the primary mode and then go away -- i.e. they are transient,  
Much like dialogs in WIMP.  
Modal apps benefit from not having to deal with windows, and layout issues.    
This results in simpler, less cluttered screens.  
They also benefit from not needing to use the mouse in the GUI.  
This improves the interaction efficiency of the UI and makes it simpler to design.  
Modal UIs are simpler and more efficient for both developer and user.  

From a WIMP app designer's perspective,  
a Modal app is like a WIMP app with a bunch of kybd short-cuts  
and mouse-based control disabled.  
i.e. all control is via kybd shortcuts.  
Since it is modal, the number of shortcuts needed per mode is small.  

This app is a source-code navigation/editing app.  
A comparison with your IDE of choice such as XCode, Visual Studio, or Code::Blocks   
in the ability to navigate code demonstrates this efficiency advantage.  
ModalWX.cpp (~500KB) is the only file needed to run the app.  
The app only caters to C/C++ Modal apps and does not have editing, build or debug features.  
Adding these features will likely double its size to 1M.  
That compares with 10s of MBytes for all of the above tools  
which demonstrates that there's a lot less code needed. 
implying simplicity for the developer.  

Modal UIs can replace WIMP GUIs in almost all cases  
except where fine-grained screen-space selection is needed  
such as graphic design tools.  

## Building and Running the app:
(interaction time ~1hr) 

The app is based on the wxWidgets cross-platform UI library.  
To build this app you first have to download and build the wxWidgets library.  

We recommend using the current development version of wxWidgets which is 3.1.6.  
You can get help for setting up wxWidgets on your PC from this forum:  
https://forums.wxwidgets.org/viewforum.php?f=19&sid=0083f4684647607be2aef5bc34b48d82.  
The build process for the library depends on your platform:  

### OSX: 
We recommend building the library from source.  
The simplest way is to download the source for wxWidgets.  
Then open %wxWidgetsDir%/Samples/minimal/minimal_cocoa.xcodeproj  
You should be able to build and run this sample in XCode.  
It builds the wxWidgets library from source as part of its build process.  
Then you can edit the project settings to replace minimal.cpp with ModalWX.cpp.  
This will build the Modal app.  

### Windows:
We recommend Visual Studio 2022 Community edition as the IDE.  
You download the source code for wxWidgets.  
Then you goto %wxWidgetsDir%/build/msw/  
You open wx_vc17.sln in Visual Studio 2022  
Build Debug and Release configurations (we recommend not dll but statically linked libraries).  
This places the built libraries in %wxWidgetsDir%/lib/vc_x64_lib (or vc_lib).  
Follow the instructions at https://forums.wxwidgets.org/viewtopic.php?p=196105#p196105  
to create a new VS project and add ModalWX.cpp to it.  
This should build the Modal app.  

### Linux:
We recommend using the CodeLite IDE which comes with wxWidgets pre-installed.  
Create a new bare-bones wxWidgets based project and add ModalWX.cpp to it.  
This should build the Modal app.  

### Running the app (UI controls):
When you run the app,  
it will ask you for the full path of ModalWX.cpp.  
This path will depend on your platform.  
Examples:  
OSX -- /Users/<username>/Modal/ModalWX.cpp  
Win -- C:\Modal\ModalWX.cpp  
Linux -- /Home/Modal/ModalWX.cpp  
![alt text](https://hex-map.khitchdee.net/ModalWX-launch-screen.png)  
If you enter an incorrect path, it will bring up a file selector  
to select the file from the file system.  
![alt text](https://hex-map.khitchdee.net/ModalWX-file-selector.png)  
The file selector has upto 5 vertical panels on the screen.  
Each panel represents the contents of a directory.  
The left most panel is closest to the root directory.  
Each panel is the contents of a directory from the left adjacent panel. 
The active panel has a white outline rectangle highlighting the current selection  
while the other panels have black outline rectangles.  
To change your selection in the active panel, use up and down arrow.  
To open a currently selected directory, use right arrow.  
To open or navigate to a parent directory, use left arrow.  
To commit to a selection (it has to be ModalWX.cpp in this case)  
press Enter(Return).  
If you make an invalid selection, it gets you back to the file selector.  
Once you have correctly selected ModalWX.cpp,  
it will load ModalWX.cpp, parse it and display its blocks.  

## ModalWX Code Walkthrough
We guide you through a sequence of steps  
that explain how this code works, how it is navigated  
and what it takes it takes to write a Modal app using this toolkit.  
The only pre-requisite is familiarity with C.  
At the end of this walkthough you will be able to evaluate  
whether ModalWX would be a better way to design your next workstation app  
compared to a WIMP-based design.  

1. Build and run the app. as described in the preceding section.  
At this point, your screen should look like this:  
![alt text](https://hex-map.khitchdee.net/ModalWX-source-loaded.png)
2. OSX font size adjustment.  
If you are on OSX, the fontsize might be too small.  
To adjust it, press the Command key once.  
This will pop-up a set of 5 options.  
Use the down arrow key to select "Adjust Fontsize".  
![alt text](https://hex-map.khitchdee.net/ModalWX-adjust-fontsize.png)  
Press Enter(Return).  
You will get a message:  
"arrows to adjust fontsize, esc to exit"  
Use the arrow keys to adjust fontsize.  
This is a live ajdustment.  
Once you are satisfied, press escape.  
3. Modal's interface with wxWidgets -- ModalWindow and the ModeManager.  
  3(a). App init  
  i. Use the down arrow key to goto line 610: WX CLASS FUNCTION DEFINITIONS{...}.  
  All lines in this color are "sub-Blocks".  
  This line is a sub-block of line 105: WX INTERFACING BOILERPLATE.  
  That line is a BLOCK. All lines in that color are BLOCKS.  
  Press Ctrl-S(Command-S on OSX) at line 610.  
  This will open the sub-block.  
  ii. Goto Line 640: bool MyApp::OnInit(){...}  
  Open the comment line just above it (line 634) and read it.  
  Then open line 640.  
  Read through this code, then close the comment -- goto line 634 and Ctrl-S.  
  Goto line 646 and move the caret to MyFrame (the one after the new).  
  Press Ctrl-Right Arrow.  
  This will take you the constructor of MyFrame.  
  Goto line 656 move the caret over ModalWindow and Ctrl-Right.  
  ModalWindow is a subclass of wxWindow defined in the sub-block WX BRIDGE STRUCTURES AND FUNCTIONS. 
  Goto line 662 and move the caret over modal_init and Ctrl-Right.  
  modal_init is where the modal toolkit is initalised.  
  Note that goto using ctrl-right goes to the destination, expands it  
  and collapses the previous viewing context.  
  Goto line 8738, this is a summarised if block.  
  open it read it and close it.  
  Any line that end in {...} is summarized and can be opened.  
  Goto line 8745 and move the caret over load_UI_state and Ctrl-Right  
  load_UI_state is an acillary fn that creates a SModeSrcEdr mode,  
  creates a SModeManager, pushes the mode onto the mode manager,   
  and returns the mode manager.  
  This is returned by modal_init() to the calling ModalWindow.  
  Now press Ctrl-Left Arrow this takes you back to modal_init.  
  Go back again (Ctrl-left) that's ModalWindow's constructor  
  Go back again that's MyFrame's constructor  
  Go back again and we're at the entry point to the app.  
  Goto line 640 and press Ctrl-S, this summarizes MyApp::OnInit().    
  So a wxWidgets app enters at MyApp::OnInit, creates a MyFrame::wxFrame
  which creates a ModalWindow::wxWindow, which contains a ModeManager  
  that serves as the interface between wxWidgets and Modal.  
  ModalWindow initializes Modal using a designated modal_init() fn.  
  The Modal App designer initializes modal in this fn by creating a primary mode  
  in this case SModeSrcEdr and pushing it into the ModeManager  
  before returning the ModeManager to ModalWindow.  
  3(b). App lifetime -- Paint and Kybd event handling  
  (i) Goto line 690 and open it.  
  This is where all key down events are handled by ModalWindow.  
  Goto line 692 and move the caret to kybd_map and Ctrl-right  
  That takes you to the modemanager's kybd_map fn.  
  This function set's pWin->m_bUsrActn which tells Modal  
  that the user has done something.  
  Then call's the kybd_map fn of the mode at the top of the mode stack  
  which is the currently active mode.  
  (ii) Go Back (Ctrl-left) and close 690.  
  Open 673 -- this is the paint event handler for ModalWindow.  
  This fn calls either disp_state if the event was not caused by the user  
  in which the state of the entire app needs to be reloaded.  
  or disp_update in which case the mode decides what needs to be updated.  
  Goto 679 and move over disp_state Ctrl-right.  
  You can read the comments inside 452.  
  Open 471, then 472, then 473.  
  The mode manager conatins a stack of modes  
  and displays each modes disp_state in back to front order (bottom to top of stack).  
  Close 473, close 472, close 471.  
  Go back.  
  Now goto 682 disp_update and Ctrl-right.  
  Open 487.  
  Note that disp_update only calls the mode at the top of the mode stack, the current mode.  
  Close 487 and go back.  
  Close 673.  
  So during execution stage, wxWidgets send key down and paint events to ModalWindow.  
  ModalWindow delegates these to the modemanager  
  which dispatches them appropriately to modes it manages.  
  3(c). App exit -- Modal shutdown and app state serialisation.    
  When a modal app is ready to exit, it tells the wxWidgets app to shutdown  
  which results in ~ModalWIndow being called.  
  Open 667. ~ModalWindow calls modal_exit().  
  Ctrl_right to modal_exit().  
  Open 8756 and then 8759.  
  modal_exit serializes the mode manager and all the modes it contains to a state file.  
  Next time the app is launched, it reads state from this file   
  to reload the last operational state of the app.  
  It also free's the mode manager which in turn free's all the modes it contains.  
  The mode manager and the modes are all created on the heap.  
  Close 8759. Close 8756 and go back.  
  Press Escape to exit the app.  
  Then relaunch the app. You should be back where you left off.    
  
4. Inside Modal -- Modes of Operation and User Intents   

## Next Development Step:
1. Modal specific navigational features and source code editing.  
2. Build.  
3. Debug.  

I estimate this will take 3 months.  

## Modal UIs and WIMP UIs
WIMP UIs have been around a long time and a broad set of controls is available  
for developers to quickly put together a UI  
with not a lot of code involved since the controls encapsulate the needed functionality.  
By contrast, writing a Modal UI requires a lot of coding  
specially in terms of graphic drawing for your app's primary mode.  
Only a few off-the-shelf controls are available to assist the process.  
On the other hand, Modal UIs give a developer a lot more control  
over the outcome of the design process since very little is canned or mandated.  
They are also simpler to code.  
Once you get familiar with the drawing options offered by wxDC,  
creating a new Mode of Operation is fairly straightforward.  
Also, in terms of visual layout they offer the entire screen for the app's display  
since there are no menus or other visual elements to take away screen space.  
The SModeSrcEdr mode demonstrates this in its ability to view large segments of code  
all on a single screen as compared WIMP based code editors  
such as XCode, Visual Studio, Code Blocks and CodeLite.  

Since a Modal UI is implemented entirely within a wxWindow subclass,  
it is possible to incorporate a Modal UI window into a regular WIMP style app  
using wxAUI with the Modal UI in one layer and a WIMP UI in another layer.  
Effectively mixing these 2 styles of UI design.  

## A note on code quality
I've only just gotten this code to work.  
There are a several hardcoded values in the code  
and several places where error conditions are not checked.  
This code has not been tested and is very brittle.  
It's posted as a proof-of-concept demo.  
However, being a source code toolkit,  
and since the code is small and relatively simple,  
you can add error-checking to core parts  
and do a lot of testing on your targeted platforms  
to make it suitable for deployment in a production environment.  
wxWidgets, which this code uses to access platform features  
is a fairly robust and well tested library.  
We also have longer-term plans to create native versions of Modal  
for C/Win32 and C/X-Windows and ObjectiveC/Cocoa  
which will remove this dependency.  

## Contributing to ModalWX
I've written about 9000 lines of code so far.  
So I have fairly stable code design, code documentation and coding conventions.  
I welcome all contributions.    
Since this is an IDE, relatively simple and has a small and well-documented codebase   
it is amenable to customisations.  
I welcome developers sharing their customisations with this project.  
Some areas where work could be done are
1. A different approach to parsing source code files.  
2. Dictionary based text input.
3. More pop-up modes to simplify not-so-common or domain-specific UI tasks.  
Some larger projects would include  
- Modal for C/Win32, which is an implementation of the Modal toolkit  
for the Win32 API.  
- Modal for C/XWindows, which is an implementation of the Modal toolkit  
for X Windows on Linux.  
- Modal for ObjectiveC/Cocoa, an implementation of the Modal toolkit in ObjectiveC  
targeting Cocoa on OSX.
