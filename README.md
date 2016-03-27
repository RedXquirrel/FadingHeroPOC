# Fading Hero POC
Scrolling the ScrollView reduces the height of the hero pane, as well as fading out some of the hero pane content (White label text, image), and fading in some new content (Black label text). 

Done in Xaml with two IValueConverters to manage opacity and masthead height changes as bound to the ScrollY value of the ScrollView

The following four images show the action of scrolling, here the yellow bar is the top of the section that is scrolled up, however notice that once it comes to the top, that the page continues to scroll upwards underneath it - this is so that a transparent image can be put in the yellow section that has a top that is not purely horizontal.

![Screenshot 1](https://github.com/Xamtastic/DiminishingMastheadPOC/blob/master/Screenshots/Screen%20Shot%201.png)

![Screenshot 2](https://github.com/Xamtastic/DiminishingMastheadPOC/blob/master/Screenshots/Screen%20Shot%202.png)

![Screenshot 3](https://github.com/Xamtastic/DiminishingMastheadPOC/blob/master/Screenshots/Screen%20Shot%203.png)

![Screenshot 4](https://github.com/Xamtastic/DiminishingMastheadPOC/blob/master/Screenshots/Screen%20Shot%204.png)

![Screenshot 4](https://github.com/Xamtastic/DiminishingMastheadPOC/blob/master/Screenshots/Screen%20Shot%205.png)
