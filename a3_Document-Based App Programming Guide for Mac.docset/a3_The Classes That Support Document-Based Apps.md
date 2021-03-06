# The Classes That Support Document-Based Apps

There are three major classes in the document architecture: NSDocumentController, NSDocument, and NSWindowController. Objects of these classes divide and orchestrate the work of creating, saving, opening, and managing the documents of an app. They are arranged in a tiered one-to-many relationship, as depicted in Figure 2-1. An app can have only one NSDocumentController object, which creates and manages one or more NSDocument objects (one for each New or Open operation). In turn, an NSDocument object creates and manages one or more NSWindowController objects, one for each of the windows displayed for a document. In addition, some of these objects have responsibilities analogous to NSApplication and NSWindow delegates, such as approving major events like closing and quitting.

**Figure 2-1**  Relationships among NSDocumentController, NSDocument, and NSWindowController objects

# 支持文档驱动应用的类

在文档架构中有三个主要的类：NSDocumentController，NSDocument以及NSWindowController。这些类的对象对创建（creating），保存（saving），打开（opening）以及管理（managing）应用的文档进行了划分和详细规划。他们被安排为一个一对多的分层关系，就像Figure 2-1中描述的。一个应用只可以拥有一个NSDocumentController对象，其创建并管理一个或多个NSDocument对象（每个都对应新建或打开操作）。依次下去，一个NSDocument对象创建并管理一个或多个NSWindowController对象，每个都对应一个显示文档的窗口。此外，这些对象中的一些拥有和NSApplication和NSWindow委托相似的责任，比如批准类似关闭（closing）和退出（quitting）之类的主事件。

**Figure 2-1** NSDocumentController，NSDocument以及NSWindowController对象之间的关系

![ Figure 2-1 ](http://i.imgbox.com/aZOHmhRO.png)


A Cocoa app includes a number of key objects in addition to the three major types of objects of the document architecture. Figure 2-2 shows how these objects fit into the overall Cocoa object infrastructure.

**Figure 2-2** Key objects in a document-based app

除了文档架构的三个主要对象之外，Cocoa应用还包含了若干个关键对象。Figure 2-2展示了这些对象如何融入到Cocoa对象的基础架构中。

**Figure 2-2** 在一个文档驱动应用中的关键对象

![ Figure 2-2 ](http://i.imgbox.com/dqVVolQG.png)


## NSDocumentController Creates and Manages Documents

An app’s NSDocumentController object manages the documents in an app. In the MVC design pattern, an NSDocumentController object is a **high-level controller.** It has the following primary responsibilities:

* Creates empty documents in response to the `New` item in the `File` menu

* Creates documents initialized with data from a file in response to the `Open` item in the `File` menu

* Tracks and manages those documents

* Handles document-related menu items, such as `Open Recent`

When a user chooses `New` from the `File` menu, the NSDocumentController object gets the appropriate NSDocument subclass from the app’s **Information property list** and allocates and initializes an instance of this class. Likewise, when the user chooses `Open`, the NSDocumentController object displays the Open dialog, gets the user’s selection, finds the NSDocument subclass for the file, allocates an instance of the class, and initializes it with data from the file. In both cases, the NSDocumentController object adds a reference to the document object to an internal list to help manage its documents.

Most of the time, you can use NSDocumentController as is to manage your app’s documents. NSDocumentController is hard-wired to respond appropriately to certain app events, such as when the app starts up, when it terminates, when the system is shutting down, and when documents are opened or printed. Alternatively, you can create a custom delegate object and implement the delegate methods corresponding to the same events (see [NSApplicationDelegate Protocol Reference](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSApplicationDelegate_Protocol/Reference/Reference.html#//apple_ref/doc/uid/TP40008592)).

## NSDocumentController用于创建和管理文档

应用的NSDocumentController对象管理着应用中的文档。在MVC设计模式中，一个NSDocumentController对象是一个**高层次控制器（high-level-controller）。**其拥有如下的主要责任：

* 创建空的文档以响应`文件`菜单中的`新建`项

* 创建文档对象并使用从文件中读取的数据进行初始化以响应`文件`菜单中的`打开`项

* 跟踪并管理那些文档

* 处理文档相关的菜单项，比如`最近打开的文档`

当一个用户从`文件`菜单中选择`新建`项时，NSDocumentController对象从应用的**Information property list**中获取适当的NSDocument子类，并分配及初始化一个该类的实例。同样地，当用户选择`打开`项时，NSDocumentController对象会显示打开对话框，获取用户的选择，从该文件中查找NSDocument的子类，分配一个该类的实例，并且使用从文件中读取的数据初始化该实例。在这两种情况中，NSDocumentController会向内部的列表中添加文档对象的引用以帮助其管理它的文档。

大多数时候，你可以使用NSDocumentController去管理你的文档。NSDocumentController是一个硬连接以妥善应对某些应用事件，比如当应用启动时，当应用终止时，当系统被关闭时，以及当文档被打开或者打印时。或者，你可以创建一个定制的委托对象并实现与同一个事件所对应的委托方法（参阅[NSApplicationDelegate Protocol Reference](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSApplicationDelegate_Protocol/Reference/Reference.html#//apple_ref/doc/uid/TP40008592)）。


## NSDocument Presents and Stores Document Data

NSDocument is the base class for document objects in the app architecture—you must create an NSDocument subclass for each type of document your app handles. When your app is running, it has an NSDocument-based object for each open document. In the MVC design pattern, NSDocument is a **model controller** because it manages the data model, that is, the persistent data associated with its document. An NSDocument object has the following responsibilities:

* Manages the display and capture of the data in its windows (with the assistance of its window controllers)

* Loads and stores (that is, reads and writes) the persistent data associated with its document

* Responds to action messages to save, print, revert, and close documents

* Runs and manages the `Save` and `Page Setup` dialogs


A fully implemented NSDocument object also knows how to track its edited status, perform undo and redo operations, print document data, and validate its menu items. Although these behaviors aren’t completely provided by default, the NSDocument object does assist the developer in implementing each, in the following ways:

* For tracking edited status, NSDocument provides a method for updating a change counter.

* For undo and redo operations, NSDocument lazily creates an NSUndoManager instance when one is requested, responds appropriately to `Undo` and `Redo` menu commands, and updates the change counter when undo and redo operations are performed.

* For printing, NSDocument facilitates the display of the `Page Setup` dialog and the subsequent modification of the NSPrintInfo object used in printing. To do this, subclasses of NSDocument must override *printOperationWithSettings:error:*.

* To validate menu items, NSDocument implements *validateUserInterfaceItem:* to manage the enabled state and titles of the menu items `Revert Document` and `Save` (which becomes `Save a Version` after the document is first saved). If you want to validate other menu items, you can override this method, but be sure to invoke the superclass implementation. For more information on menu item validation, see [NSUserInterfaceValidations Protocol Reference.](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSUserInterfaceValidations_Protocol/Reference/Reference.html#//apple_ref/doc/uid/TP40004183)

When designing your document objects, you should always maintain a clean separation between these data-handling activities of the document object itself and the code for managing the visual presentation of that data. The document object is responsible for the data, including the reading and writing of that data to disk. The visual presentation of that data is the responsibility of the associated window controller object. Keeping a clean separation between these two activities makes for a more modular design that can be updated more easily in the future.

Nonetheless, managing the document’s data and its user interface are closely related, which is why the document object owns and manages its window controllers. The document object also manages its menu, which is part of the user interface, because the state of its user commands—what commands are available and whether they are enabled—is determined by the state of the document data.

An NSDocument object should not contain or require the presence of any objects that are specific to the app’s user interface. Although a document can own and manage NSWindowController objects—which present the document visually and allow the user to edit it—it should not depend on these objects being there. For example, it might be desirable to have a document open in your app without having it visually displayed.

For details about subclassing NSDocument, see *“Creating the Subclass of NSDocument.”*

If you have a large data set or require a managed object model, you may want to use NSPersistentDocument, a subclass of NSDocument, to create a document-based app that uses Core Data. For more information, see *Core Data Starting Point.*



## NSDocument提供并存储文档数据

NSDocument是应用程序架构中的文档对象的基类——你必须为你的应用处理的每个文档类型都创建一个NSDocument子类。当你的应用运行时，它会为每个打开的文档创建一个基于NSDocument的对象。在MVC设计模式中，NSDocument是一个**模型控制器**，因为它管理数据模型，即与它的文档相关联的持久化数据。一个NSDocument对象拥有如下的责任：

* 管理数据在其窗口中（用它的窗口控制器来辅助）的显示和捕捉

* 加载和存储（即读取和写入）与它的文档相关联的持久化数据

* 响应动作消息以保存，打印，还原，以及关闭文档

* 运行和管理`保存`和`页面设置`对话框

一个完整的NSDocument实现应该还知道如何跟踪它的编辑状态，制定撤销（undo）和重做（redo）操作，打印文档数据，以及验证它的菜单项。尽管缺省情况下这些行为并不被完整提供，但是NSDocument对象使用一下几种途径来帮助开发者实现每一个功能：

* 对于跟踪编辑状态，NSDocument提供了一个用于更新**更改计数器（change counter）**的方法

* 对于撤销和重做操作，NSDocument会在其第一次被请求时惰性创建一个NSUndoManager实例，妥当响应`撤销`和`重做`菜单命令，并且当撤销和重做操作被执行时更新**更改计数器**

* 对于打印，NSDocument使`页面设置`对话框和NSPrintInfo对象的随后的更改被用于打印都变得很容易。为此，NSDocument的子类必须覆写*printOperationWithSettings:error:*方法

* 对于验证菜单项，NSDocument实现了*validateUserInterfaceItem:*方法以管理菜单项`还原文档`和`保存`（其在文档第一次被保存后会变成`保存一个版本`）的启用状态及标题。如果你想要验证其他菜单项，你可以覆写该方法，但是要保证调用其超类实现。更多关于菜单项验证的信息，参阅[NSUserInterfaceValidations Protocol Reference.](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSUserInterfaceValidations_Protocol/Reference/Reference.html#//apple_ref/doc/uid/TP40004183)


当设计你的文档对象时，你应该总是在**这些文档对象自身的数据操作行为**与**该数据的可视化展现的管理代码**之间保持清晰的分离。文档对象负责数据，包括在磁盘中对该数据的读操作和写操作。该数据的可视化展现则负责与窗口控制器对象关联。在这两个行为之间保持清晰的分离，有助于更加模块化的设计，这种设计会使未来的更新变得更容易。

尽管如此，管理文档的数据和它的用户界面还是莫切相关的，这就是为什么文档对象拥有并管理它的窗口控制器。文档对象也管理它的菜单，其菜单作为用户界面的一部分，这是因为它的用户命令的状态——哪个命令可用以及它们是否被启用——是由文档数据的状态来决定的。

一个NSDocument对象不应该包含或者需要任何特定于应用程序用户界面的对象存在，尽管一个文档对象可以拥有并管理NSWindowController对象——其以可视化的方式提供了文档并且允许用户去编辑文档——但是它不应该依赖于这些对象。例如，你的应用拥有一个打开的文档但是并没有可视化的显示也是合理的。

关于子类化NSDocument的详细信息，参阅*“Creating the Subclass of NSDocument。”*

如果你拥有一个庞大的数据集或者需要一个托管对象模型，你可能想要使用NSPersistentDocument，一个NSDocument的子类，用于创建使用Core Data的文档驱动应用程序。更多的信息参阅*Core Data Starting Point.*



## NSWindowController Manages One Document Window

An NSWindowController object manages one window associated with a document. That window is typically stored in a nib file. As such, in the MVC design pattern, it is a **view controller.** When an NSWindowController object receives a request from its owning NSDocument object, it loads the nib file containing a window, displays the window, and sets itself as the File’s Owner of the nib file. It also assumes responsibility for closing windows properly.

A window controller keeps track of its window using its window outlet. The window outlet should be connected to the window for which your window controller is responsible, as shown in Figure 2-3.

**Figure 2-3**  Window outlet of window controller

Although not required, it’s often convenient to set up your window controller as the delegate of the window it manages. In your nib file, connect the delegate outlet of the window your window controller is managing to the object that represents your window controller—specifically, the File’s Owner object.

> Note: NSWindowController does not depend on being the controlled window's delegate to do its job, and it doesn't implement any NSWindow delegate methods. A subclass of NSWindowController, however, is a fine place to put implementations of NSWindow delegate methods, and if you do so you'll probably need to connect the delegate outlet of the window to the File's Owner of the nib file as described. But you do not have to do so for NSWindowController itself to work properly.

The Xcode document-based app template does not subclass NSWindowController, and you do not need to do so if you are writing a simple app. However, if you are writing an app with more advanced requirements, as is typical, you will almost certainly want to do so. In addition, subclassing NSWindowController promotes better encapsulation of your view and model code. For more information, see *“You Should Subclass NSWindowController.”*



## NSWindowController管理一个文档窗口

一个NSWindowController对象管理与某一个文档关联的窗口。该窗口通常存储在一个nib文件中。就其本身而言，在MVC设计模式中，它是一个**视图控制器**。当一个NSWindowController对象从拥有其的NSDocument对象接收一个请求时，它就会加载包含窗口的nib文件，显示该窗口，并且设置它自己为该nib文件的File's Owner。它也会承担正确关闭窗口的责任。

一个窗口控制器使用它的窗口插座（outlet）来保持对它的窗口的追踪。窗口插座应该被连接到你的窗口控制器所负责的那个窗口上，如Figure 2-3所示。

**Figure 2-3**  窗口控制器的窗口插座（outlet）

![ Figure 2-3 ](http://i.imgbox.com/3XxaXLlQ.png)

即使不是必须的，但通常还是很方便地将**你的窗口控制器**设置为**其管理的窗口**的委托。在你的nib文件中，将**你的窗口控制器所管理的窗口**的委托插座连接到代表你的窗口控制器的对象——明确地说，是File' Owner对象。

> 注意：NSWindowController并不是一定要成为其所控制的窗口的委托才能履行自己的职责，并且它也没有实现任何NSWindow委托方法。然而，NSWindowController子类的确是一个放置NSWindow委托实现的好地方，并且如果你这么做了你可能需要如前所述地将窗口的委托插座连接到nib文件的File' Owner。但是并不是一定要做这些才能让NSWindowController正确地工作。

Xcode的文档驱动应用模板并不会子类化NSWindowController，而且如果你在编写一个简单的应用那么其实并不需要子类化它。然而，如果你正在编写一个带有更多高级需求的应用，你几乎肯定相会子类化NSWindowController。此外，子类化NSWindowController会促进视图和模型代码更好地封装。更多信息，参阅*“You Should Subclass NSWindowController。”*



## Subclassing Objects in the Document Architecture

You can create a document-based app without writing much code. You have only to create a document project, compose the human interface, implement a subclass of NSDocument, and add any other custom classes or behavior required by your app. However, most app requirements are more complex, so you can customize the default object architecture through subclassing and delegation, as described in this section.

Table 2-1 summarizes the object architecture and subclass requirements of a document-based app.

**Table 2-1**  Document architecture objects and subclasses

Class                   | Number of objects | Subclassing
:---------------------- | :---------------  | :--------------------------
NSDocument              | 1 per document    | Required
NSWindowController      | 1 per window      | Optional (but recommended)
NSDocumentController    | 1 per app         | Optional (and unlikely)

你可以不编写很多代码而创建一个文档驱动型应用。你可以只创建一个文档项目，构建用户界面，实现NSDocument子类以及添加任何你的应用所需要的其他定制类或行为。然而，大多数应用的需求会更加复杂，所以你可以通过子类化和委托来定制默认对象架构，如该章节所描述

Table 2-1总结了文档驱动型应用的对象架构和子类化需求。

**Table 2-1**  文档架构的对象和子类化

类                      | 对象数量            | 子类化
:---------------------- | :---------------  | :--------------------
NSDocument              | 每个文档1个         | 必要
NSWindowController      | 每个窗口一个        | 可选（推荐）
NSDocumentController    | 每个应用一个        | 可选（不太可能需要）



### You Must Subclass NSDocument
Every app that uses the document architecture must create at least one subclass of NSDocument. To create a document-based Cocoa app, you choose the Xcode template for a Cocoa application presented in the `New Project` dialog and select the option `Create Document-Based Application` in the next pane. When you do this, you get a new app project that already contains a subclass of NSDocument and nib files for your document and app menu. Minimal or empty method implementations are provided for:

* **Reading and writing document data.** Comments explain what you need to fill in, how to handle an error condition, and alternate reading and writing methods to override instead. The method bodies include code that throws an “unimplemented method” exception if you don’t change anything.

* **Initialization of the document object.** The implementation contains the proper Cocoa initialization pattern, which calls the superclass initializer and provides a place for subclass-specific initialization.

* **Returning the document nib file name.** This code overrides the *windowNibName* method to return the nib file name used for documents of this type. Comments explain situations where you should do alternate overrides.

* **Post-nib-loading code.** This override provides a place for code to be executed after the document window nib file is loaded. For example, objects in the nib cannot be initialized until after the nib is loaded.

* **Opting into autosaving.** By leaving this override as written in the template to return YES, you ensure that your document saves its data to disk automatically.

See *“Creating the Subclass of NSDocument”* for information about implementing the required methods in your NSDocument subclass.

### 你必须子类化NSDocument
每个使用文档架构的应用都必须创建至少一个NSDocument的子类。要创建一个文档驱动的Cocoa应用，你可以在`New Project`对话框中为Cocoa应用程序提供的Xcode模板，在下一个面板中并选择`Create Document-Based Application`选项。当你这样做后，你会获得一个新的应用项目，其中已经包含了一个NSDocument子类和为你的文档和应用菜单创建的两个nib文件。一下几个方法提供了最少的或者空的方法实现：

* **读写文档数据。** 注释解释了你应该填入什么，如何处理错误情况，以及替换读写方法以覆写方法。方法体包含了如果你没有更改任何事物就会抛出一个“unimplemented method”异常的代码。

* **文档对象的初始化。** 该实现包含了Cocoa特有的初始化模式，其调用超类的初始化器，并且为特定于子类的初始化提供了位置。

* **返回文档nib文件的名称。** 该代码重写了*windowNibName*方法以返回用于该类型文档的nib文件名称。注释解释了你应该替换该覆写的情境。

* **布置nib加载代码（Post-nib-loading code)。** 该覆写为那些在文档窗口nib文件被加载后被执行的代码提供了位置。比如说，在nib文件中的对象直到nib文件被加载后才会被初始化。

* **选择autosaving。** 通过在模板中编写并返回YES来留下该覆写，你可以确保你的文档会自动将其数据保存到磁盘。

参阅*“Creating the Subclass of NSDocument”*以获得关于实现你的NSDocument子类必要方法的信息。



### You Should Subclass NSWindowController
Even if your document has only one window, it may be complex enough that you’d like to split up some of the logic in the controller layer to have a view controller as well as a model controller object. In this case, you should subclass NSWindowController as well as NSDocument. In this way, you can add specific knowledge of the app’s view layer that the window controller is responsible for managing. Any outlets and actions, and any other behavior that is specific to the management of the user interface, goes into the NSWindowController subclass. Especially for larger apps, splitting the controller duties between two classes makes a lot of sense. This strategy allows you to have documents that are open, but not onscreen, to avoid having to allocate memory and other resources of a front-end that may not be used in some circumstances.

### 你应该子类化NSWindowController
即使你的文档只有一个窗口，但是如果你想在控制器层中划分出一些逻辑以拥有视图控制器和模型控制器，还是会变得足够复杂。在这种情况中，你应该子类化NSWindowController和NSDocument。这样，你就可以添加应用的窗口控制器所负责管理的视图层的特定认知。任何插座（outlets）和动作（actions），以及任何特定于用户界面管理的行为都应该放入NSWindowController的子类。尤其是大型的应用，在两个类之间划分控制器的责任具有重大意义。该策略允许你使**那些被打开但是并没有在屏幕上显示的文档**避免在某些情况中被分配可能不会被用到的内存和前端资源。

---

#### Reasons to Subclass NSWindowController
If your document requires or allows multiple windows for a single document, that is a good reason to subclass NSWindowController. For example, a CAD program could need to present front, top, and side views, as well as a rendered 3D view of a document. When it does, you might want to have one or more subclasses of NSWindowController to manage the different kinds of windows that your document needs, and so you must create one of each in *makeWindowControllers.*

Some apps need only one window for a document but want to allow the user to create several copies of the window for a single document (sometimes this is called a multiple-view document) so that the user can have each window scrolled to a different position or displayed differently, such as at a different scale. In this case, your *makeWindowControllers* override would create only one NSWindowController object, and there would be a menu command or other control that allows the user to create others.

Another reason to subclass NSWindowController is to customize your document window titles. To customize a document's window title properly, subclass NSWindowController and override *windowTitleForDocumentDisplayName:*. If your app requires even deeper customization, override *synchronizeWindowTitleWithDocumentName*.

#### 子类化NSWindowController的原因
如果你的文档需要或者允许一个文档对应多个窗口，那么这就是一个NSWindowController的好原因。比如说，一个CAD程序需要提供文档的前部，顶部和侧部视图，以及一个3D渲染视图。当这样时，你可能想要一个或多个NSWindowController的子类以管理你的文档所需要的不同种类的窗口，并且你必须在*makeWindowContorller*方法中挨个创建。

一些应用只需要一个窗口对应一个文档，但是想要允许用户能够为一个文档创建该窗口的多份副本（有时候这叫做多视图文档），以便于用户可以使每个窗口都滚动到不同的位置或者以不同的方式显示，比如在不同的缩放下显示。在这种情况下，你的*makeWindowControllers*覆写将会只创建一个NSWindowController对象，并且将会有一个菜单命令或者其他允许用户创建其他窗口的NSControl。

另一个子类化NSWindowController的原因就是要定制你的文档窗口的标题。要正确地定制一个文档窗口的标题，子类化NSWindowController并覆写*windowTitleForDocumentDisplayName:*方法。如果你的应用需要更深一步地定制，那么覆写*synchronizeWindowTitleWithDocumentName*方法。

---

#### How to Subclass NSWindowController
Once you've decided to subclass NSWindowController, you need to change the default document-based app setup. First, add any Interface Builder outlets and actions for your document's user interface to the NSWindowController subclass instead of to the NSDocument subclass. The NSWindowController subclass instance should be the File’s Owner for the nib file because that creates better separation between the view-related logic and the model-related logic. Some menu actions can still be implemented in the NSDocument subclass. For example, `Save` and `Revert Document` are implemented by NSDocument, and you might add other menu actions of your own, such as an action for creating new views on a document.

Second, instead of overriding *windowNibName* in your NSDocument subclass, override *makeWindowControllers*. In *makeWindowControllers*, create at least one instance of your custom NSWindowController subclass and use *addWindowController:* to add it to the document. If your document always needs multiple controllers, create them all here. If a document can support multiple views but by default has one, create the controller for the default view here and provide user actions for creating other views.

You should not force the windows to be visible in *makeWindowControllers*. NSDocument does that for you if it’s appropriate.

#### 如何子类化NSWindowController
一旦你已经决定子类化NSWindowController，你需要更改默认的文档驱动型应用的设置。首先，将任何与你的文档用户界面有关的Interface Builder插座（outlets）和动作（actions）添加到NSWindowController子类中，而不是NSDocument子类中。NSWindowController子类实例应该成为nib文件的File's Owner，因为这会在视图相关的逻辑（view-related logic）和模型相关的逻辑（model-related logic）之间创建更好的划分。一些菜单动作仍然可以在NSDocument子类中被实现。比如`保存`和`还原文档`就会由NSDocument来实现，并且你可以添加其他你拥有的菜单动作，比如一个用于在文档上创建新视图的动作。

其次，在你的NSDocument子类中覆写*makeWindowControllers*方法，而不是*windowNibName*方法。在*makeWindowControllers*中，应该创建至少一个定制NSWindowController子类的实例，并使用*addWindowController:*方法来将其添加到文档对象中。如果你的文档总是需要多个控制器，那么在这里全部创建它们。如果一个文档支持多个视图但是默认情况下只显示一个，那么在这里为默认的视图创建控制器并提供创建其他视图的用户动作。

你不应该在*makeWindowControllers*方法中强制窗口可见。NSDocument会在合适是的时候为你这样做。

---

#### An NSWindowController Subclass Manages Nib Files
An NSWindowController object expects to be told what nib file to load (through its *initWithWindowNib...* methods) because it is a generic implementation of the default behavior for all window controllers. However, when you write a subclass of NSWindowController, that subclass is almost always designed to control the user interface contained in a particular nib file, and your subclass would not work with a different nib file. It is therefore inconvenient and error-prone for the instantiator of the subclass to have to tell it which nib file to load.

This problem is solved by overriding the *init* method to call the superclass’s *initWithWindowNibName:* method with the correct nib name. Then instantiators just use *init*, and the controller has the correct nib file. You can also override the *initWithWindowNib...* methods to log an error, as shown in Figure 2-4, because no instantiator should ever try to tell your subclass which nib file to use. It is a good idea for any NSWindowController subclass designed to work with a specific nib file to use this technique. You should do otherwise only if you are extending just the basic functionality of NSWindowController in your subclass and have not tied that functionality to any particular nib file.

**Figure 2-4**  Loading a nib file that is controller specific

An NSWindowController object without an associated NSDocument object is useful by itself. NSWindowController can be used as the base class for auxiliary panel controllers in order to gain the use of its nib management abilities. One common standalone use of NSWindowController subclasses is as controllers for shared panels such as find panels, inspectors, or preferences panels. For example, the Sketch sample app uses NSWindowController subclasses for its various secondary panels. In this case, you can make an NSWindowController subclass that implements a “shared-instance” method to create a singleton window controller object. For example, you could create a PreferencesController subclass with a *sharedPreferenceController* class method that creates a single instance the first time it is called and returns that same instance on all subsequent calls.

Because your subclass derives from NSWindowController, you can just tell it the name of your preferences nib file and it handles loading the nib file and managing the window automatically. You add your own outlets and actions, as usual, to hook up the specific user interface for your panel and add methods to manage the panel’s behavior.

#### NSWindowController子类管理nib文件
一个NSWindowController对象期望被告知哪个nib文件会被加载（通过它的*initWithWindowNib...*方法）因为这是一个所有窗口控制器默认行为的范型实现。然而，当你编写一个NSWindowController的子类时，该子类几乎总是被设计为控制特定nib文件中的用户界面，并且你的子类将不会与不同的nib文件一起工作。当不得不告知子类的初始化器那一个nib文件要被加载时，初始化操作就会产生不变和易错。

这个问题通过覆写*init*方法以用正确的nib文件名调用超类的*initWithWindowNib:*方法来解决。然后初始化器可以只使用*init*方法，并且控制器会拥有正确的nib文件。你还可以覆写*initWithWindowNib...*方法来记录错误，如Figure 2-4所示，因为没有初始化器应该试图告诉你的子类那一个nib文件被使用。对于任何被设计用于与特定nib文件工作的NSWindowController子类，使用该技巧是一个好主意。只当你在你的子类中只是扩展NSWindowController的基础功能时，你才应该不这样做，并且不需要将功能与任何特定的nib文件捆绑。

**Figure 2-4**  加载特定于控制器的nib文件

![ Figure 2-4 ](http://i.imgbox.com/Lg1vhorO.png)

一个不关联到任何NSDocument对象的NSWindowController对象本身也是有用的。NSWindowController可以被用作辅助面板控制器的基类，以便获得nib使用的管理能力。一个通用的NSWindowController子类的独立用法是作为诸如查找面板，检视器，或者偏好设置面板之类的共享面板的控制器。例如，Sketch事例应用为它的各种次要面板使用NSWindowController子类。在这种情况中，你可以创建一个实现了“共享实例”方法的NSWindowController子类以创建单例模式的窗口控制器对象。例如，你可以使用*sharedPreferenceController*方法来创建一个PreferenceController子类，该方法在第一次被调用时创建一个单一实例，并在随后的所有调用中返回相同的实例。

因为你的子类继承自NSWindowController，所以你可以只告诉该类你的偏好面板nib文件的名字，并且其自动处理加载nib文件和管理窗口。像往常一样添加你拥有的插座和动作，将你的面板连接到指定用户界面并添加方法以管理面板的行为。

---

#### You Rarely Need to Subclass NSDocumentController
Most apps do not need to subclass NSDocumentController. Almost anything that can be done by subclassing can be done just as easily by the app’s delegate. However, it is possible to subclass NSDocumentController if you need to.

For example, if you need to customize the `Open` dialog, an NSDocumentController subclass is needed. You can override the NSDocumentController method *runModalOpenPanel:forTypes:* to customize the dialog or add an accessory view. The *addDocument:* and *removeDocument:* methods are provided for subclasses that want to know when documents are opened or closed.

There are two ways to subclass NSDocumentController:

* You can make an instance of your subclass in your app’s main nib file. This instance becomes the shared instance.

* You can create an instance of your subclass in your app delegate’s *applicationWillFinishLaunching:* method.

The first NSDocumentController object to be created becomes the shared instance. The AppKit framework creates the shared instance (using the NSDocumentController class) during the “finish launching” phase of app startup. So if you need a subclass instance, you must create it before AppKit does.

#### 你极少地需要子类化NSDocumentController
大多数应用不需要子类化NSDocumentController。几乎任何可以通过子类化做到的事情，都可以通过应用的委托更简单地做到。然而，如果你需要的话，还是有可能去子类化NSDocumentController的。

例如，如果你需要定制`Open`对话框，那么一个NSDocumentController子类时需要的。你可以覆写NSDocumentController方法*runModalOpenPanel:forTypes:*以定制对话框或者添加附件视图。*addDocument:*和*removeDocument:*方法提供给那些想要知道文档何时被打开或关闭的子类。

有两种子类化NSDocumentController的途径：

* 你可以在应用的主nib文件中创建你的子类的实例。该实例会成为共享实例。

* 你可以在你的应用的委托的*applicationWillFinishLaunching:*方法中创建你的子类的实例。

第一个NSDocumentController对象被创建成为共享实例。AppKit框架会在应用的启动阶段创建该共享实例（使用NSDocumentController类）。所以如果你需要一个子类化实例，你必在AppKit做这件事之前须创建它。





