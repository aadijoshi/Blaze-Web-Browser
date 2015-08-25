import sys
from PyQt4 import QtGui, QtCore
from PyQt4.QtWebKit import *

class Browser(QtGui.QMainWindow):
    def __init__(self):
        QtGui.QMainWindow.__init__(self)
        self.setWindowTitle("Blaze Web Browser")
        self.initialize()

    def initialize(self):
        #back,forward, and reload functions cannot be called directly, which is why a seperate method is created
        backAction = QtGui.QAction(QtGui.QIcon("back.png"),"Back",self)
        backAction.triggered.connect(self.back)

        forwardAction = QtGui.QAction(QtGui.QIcon("forward.png"),"Forward",self)
        forwardAction.triggered.connect(self.forward)

        reloadAction = QtGui.QAction(QtGui.QIcon("reload.png"),"Reload",self)
        reloadAction.triggered.connect(self.loadAgain)

        deleteAction = QtGui.QAction(QtGui.QIcon("delete2.png"),"Delete Duplicates",self)
        deleteAction.triggered.connect(self.deleteDuplicates)

        #create url entry bar
        self.urlBar = QtGui.QLineEdit()

        #create menubar
        self.menubar = QtGui.QMenuBar()
        fileMenu = self.menubar.addMenu('&File')
        newWindowAction = QtGui.QAction("New Window", self)
        newWindowAction.triggered.connect(self.newWindow)
        #fileMenu.addAction(newWindowAction)
        newTabAction = QtGui.QAction("New Tab", self)
        newTabAction.triggered.connect(self.newTab)
        fileMenu.addAction(newTabAction)
        switchAction = QtGui.QAction("Switch", self)
        switchAction.triggered.connect(self.switchAction)
        fileMenu.addAction(switchAction)
        
        #create toolbar
        self.toolbar = self.addToolBar("Main Toolbar")
        self.toolbar.setFloatable(False)
        self.toolbar.setMovable(False)
        self.toolbar.setFixedHeight(25)
        #self.toolbar.setStyleSheet("QToolBar {background:rgb(220,220,220); border: 1 px solid black}")
        self.toolbar.addAction(backAction)
        self.toolbar.addAction(forwardAction)
        self.toolbar.addAction(reloadAction)
        #self.toolbar.addAction(deleteAction)
        self.toolbar.addWidget(self.urlBar)

        #create tabbar
        self.tabBar = QtGui.QTabWidget()
        self.tabBar.setTabsClosable(True)
        self.setStyleSheet("QMainWindow {background:qlineargradient(x1:0 y1:0, x2:0 y2:1, stop:0 white, stop:1 gray); border: 0 px solid black}")
        #self.setStyleSheet("QMainWindow {background:rgba(255,255,255,80%); border: 0 px solid black}")
        self.listOfWebViews = []
        cornerButton = QtGui.QPushButton('Add Tab', self.tabBar)
        cornerButton.clicked.connect(self.newTab)
        self.tabBar.setCornerWidget(cornerButton)
        

        self.stack = QtGui.QStackedWidget()
        self.stack.addWidget(self.tabBar)

        #make stuff transparent
        '''
        self.toolbar.setStyleSheet("QToolBar {background:transparent; border: 1 px solid black}")
        self.tabBar.setStyleSheet("QTabBar {background:transparent; border: 0 px solid black}")
        self.setStyleSheet("QMainWindow {background:transparent; border: 0 px solid black}")
        '''

        #create web view
        self.newTab()

        #connect pressing enter with loading webpage, connect wouldn't work with arguments, so using urlBar text directly
        self.urlBar.returnPressed.connect(self.loadUrl)
        #self.activeWebView.urlChanged.connect(self.updateBar)
        self.tabBar.currentChanged.connect(self.changeActiveWebView)
        self.tabBar.tabCloseRequested.connect(self.removeTab)
        
        
        #set webpage as central content (otherwise won't show)
        self.setCentralWidget(self.stack)
        self.move(0,0)
        self.show()

    def loadUrl(self):
        '''
        Load requested URL
        '''
        text = self.urlBar.text()
        #create list of top level domains
        fp = open("topLevelDomains.txt")
        listOfDomains = []
        for line in fp:
            line = line.strip()
            listOfDomains.append("."+line.lower())
        #check if toplevel domain in url
        domainInText = False
        for domain in listOfDomains:
            if domain in text or domain.upper() in text:
                print("Yes")
                if domain+"/" in text or domain+"." in text or text[(-len(domain))::] == domain:
                    print(domain)
                    domainInText = True
                elif domain.upper()+"/" in text or domain.upper()+"." in text or text[(-len(domain.upper()))::] == domain.upper():
                    domainInText = True

        #correctly format as query or url request
        if text[0:11] == "http://www." and domainInText or text[0:12] == "https://www." and domainInText:
            pass
        elif text[0:7] == "http://" and domainInText or text[0:8] == "https://" and domainInText:
            if text[0:7] == "http://":
                text = "http://www."+text[8::]
            elif text[0:8] == "https://":
                text = "https://www."+text[9::]
        elif text[0:4] =="www." and domainInText:
            self.urlBar.setText("http://"+text)
        elif domainInText:
            self.urlBar.setText("http://www."+text)
        else:
            #format as query
            text.replace(" ","+")
            self.urlBar.setText("http://www.google.com/#q="+text)

        url = QtCore.QUrl(self.urlBar.text())
        #self.webView.load(url)
        self.activeWebView.load(url)
        #self.activeWebView.loadFinished.connect(self.setTabText)
        self.activeWebView.loadProgress.connect(self.setTabText)
        self.updateBar()

    def changeActiveWebView(self,index):
        self.activeWebView = self.listOfWebViews[index]
        self.updateBar()

    def newTab(self):
        webView = QWebView()
        self.activeWebView = webView
        self.urlBar.setText("http://www.google.com/ncr")
        self.loadUrl()
        self.listOfWebViews.append(self.activeWebView)
        self.tabBar.addTab(self.activeWebView,str("Google"))
        self.tabBar.setCurrentIndex(self.tabBar.count()-1)
        self.activeWebView.urlChanged.connect(self.updateBar)
        self.setStyleSheet("QMainWindow {background:qlineargradient(x1:0 y1:0, x2:0 y2:1, stop:0 white, stop:1 gray); border: 0 px solid black}")
        
    def updateBar(self):
        url = self.activeWebView.url()
        self.urlBar.setText(url.toString())
        #title = self.activeWebView.title()
        #print("Called",str(title))
        #self.tabBar.setTabText(self.tabBar.currentIndex(), self.activeWebView.title())

    def back(self):
        '''
        Move back through visited webpages
        '''
        self.activeWebView.back()

    def forward(self):
        '''
        Move forward through visited webpages
        '''
        self.activeWebView.forward()

    def loadAgain(self):
        '''
        Reload webpage
        '''
        self.activeWebView.reload()

    def switchAction(self):
        if self.stack.currentIndex() == 0:            
            self.cal = QtGui.QCalendarWidget()
            self.stack.addWidget(self.cal)
            self.stack.setCurrentIndex(1)
        else:
            self.stack.removeWidget(self.cal)

    def setTabText(self, progress = -1000):
        if progress == -1000 or progress >= 1:
            self.tabBar.setTabText(self.tabBar.currentIndex(), self.activeWebView.title())
            #self.updateBar()
            
    def removeTab(self, index):
        self.tabBar.removeTab(index)
        if len(self.listOfWebViews) > 1:
            del self.listOfWebViews[index]
            self.activeWebView = self.listOfWebViews[index-1]
            self.updateBar()
        else:
            del self.listOfWebViews[index]
            self.urlBar.setText("Please Open a Tab to Begin")
            self.setStyleSheet("QMainWindow {background:rgba(255,255,255,0%); border: 0 px solid black}")

    def newWindow(self):
        newBrowser = Browser()

    def deleteDuplicates(self):
        #list1 = self.listOfWebViews
        #list2 = self.tabBar.index?
        print("Hello")

        listOfUrls = []

        for webPage in self.listOfWebViews:
            url = webPage.url()
            string = url.toString()
            listOfUrls.append(string)

        print(listOfUrls)
        
        for url in range(0,len(listOfUrls)-1):
            listOfIndices = []
            for urls in range(0,len(listOfUrls)-1):
                if listOfUrls[url] == listOfUrls[urls] and url != urls:
                    print("worked")
                    listOfIndices.append[urls]
            for index in listOfIndices:
                del listOfWebViews[index]
                self.removeTab(index)
                    

if __name__ == "__main__":
    #create instance of application class, required by all PyQt4 applications
    application = QtGui.QApplication(sys.argv)
    browser = Browser()
    sys.exit(application.exec_())
