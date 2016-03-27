# Fading Hero POC
In this Proof-of-Concept, scrolling the ScrollView reduces the height of the hero pane, as well as fading out some of the hero pane content (White label text, image), and fading in some new content (Black label text). Once the hero is at it's minimum height, the scroll-pane continues to scroll under it. Note that before fading, the hero title is uppercase, but after scrolling, it is lowercase.

Done in Xaml with two IValueConverters to manage opacity and hero height changes as bound to the ScrollY value of the ScrollView.

###### It all starts with a ScrollView, vis-a-vis:

```
    <ScrollView x:Name="MaskScrollView" Grid.Row="1" Grid.RowSpan="4">
      <StackLayout StackLayout.Spacing="0">
        <BoxView HeightRequest="{StaticResource HeroHeightKey}" Color="Transparent" />
        <Image Source="transparentscrollviewlip.png" Aspect="Fill" HorizontalOptions="StartAndExpand" HeightRequest="30" />
        ...
```

The StackLayout in the ScrollView holds the 'scrolling pane' of the ScrollView that the user gestures upwards and downwards ... but notice that the first BoxView in the 'scroll-pane' is transparent - this affords the underlying hero image to be seen, and so assures a 'space' below where the first scrolling content is seen to start. In this case, the first thing that scrolls, is the scrolling 'lip' image, which is followed by content.

So, where FIRST was the ScrollView, SECOND is the BoxView:

```
        <BoxView HeightRequest="{StaticResource HeroHeightKey}" Color="Transparent" />
```

... and THIRD is the scrollview 'lip' image, whose purpose is to give a cue to the user when the Hero image is in it's 'closed' state, it is closed, that they should swipe down to 'open' the hero image and in so doing, reveal it's hidden content.

```
<Image Source="transparentscrollviewlip.png" ...
```

The ScrollView 'lip' image itself looks like this (one of the iOS ones, in this case), the top left white area of the rectangle is the transparent area, and the blue area is the colour of the page below it:

![ScrollView Lip](https://raw.githubusercontent.com/Xamtastic/FadingHeroPOC/master/POC/POC.iOS/Resources/transparentscrollviewlip%403x.png)

Notice, in particular, the HeroHeightKey that is used as the BoxView's HeightRequest - this value is needed by a few ui elements to coordinate the visual effect, hence this key exists for each of the coordinating ui elements to reference in the page's ResourceDictionary ... just change the value of this key and the height of the transparent BoxView is changed, and all of the coordinating elements follow suit:

```
    <ResourceDictionary>

      <x:Double x:Key="HeroHeightKey">275</x:Double>
      
      <converters:MaskConverter x:Key="MaskConverter" />
      
      <converters:ScrollViewingFadingOpacityConverter x:Key="ScrollViewingFadingOpacityConverter" />
      <converters:ScrollViewingUnfadingOpacityConverter x:Key="ScrollViewingUnfadingOpacityConverter" />
      
    </ResourceDictionary>
```

The HeroHeightKey is used in many places that reference the ScrollY value of the ScrollView, and here you can see the value passing through to the IValueConverter that is used by the Hero image to fade:

```
    <Image 
                  Grid.Row="0" Grid.RowSpan="4" Source="oak.png" Aspect="AspectFill"  
                  BindingContext="{x:Reference Name=MaskScrollView}"
                  Opacity="{Binding ScrollY, 
                    Converter={StaticResource ScrollViewingFadingOpacityConverter},
                    ConverterParameter={StaticResource HeroHeightKey}
                    }"  
                    />
```

And which is worked upon by one of the converters, eg:

```
    public class ScrollViewingFadingOpacityConverter : IValueConverter
    {
        public object Convert(object value, Type targetType, object parameter, System.Globalization.CultureInfo culture)
        {
            double input = System.Convert.ToDouble(value);
            double scale = System.Convert.ToDouble(parameter);

            double result = (scale - input) / scale;

            return result;
        }

        public object ConvertBack(object value, Type targetType, object parameter, System.Globalization.CultureInfo culture)
        {
            throw new NotImplementedException();
        }
    }
```

Reverse Binding (that is, the references that are made to the ScrollView's ScrollY property) is made possible by setting the BindingContext of each of these elements to the name of the ScrollView itself:

```
BindingContext="{x:Reference Name=MaskScrollView}"
```

In this way, the Opacity of the fading elements can be set to the ScrollView's ScrollY property, and this value is returned by the converter to be between 0 and 1, vis-a-vis:

```
        public object Convert(object value, Type targetType, object parameter, System.Globalization.CultureInfo culture)
        {
            double input = System.Convert.ToDouble(value);
            double scale = System.Convert.ToDouble(parameter);

            double result = (scale - input) / scale; // i.e. between 0 and 1

            return result;
        }
```

You will, of course, notice that there are TWO images that reference the ScrollView 'lip' transparent image!

Where you have already seen the one in the ScrollView that moves upwards and downwards, according to thge user's upwards or downwards gesture, the second one is positioned to trick the eye, whereas the first one continues to scroll upwards as the scrollview scrolls upwards, the second one becomes visible in a stationary position at that very moment that the one scrolling upwards, scrolls beneath it. Here, the eye is tricked intop thinking tha the one scrolling upwards, has stopped!

Conversely, the 'stationary' lip becomes invisible when the scrollview scrolls the other way and the 'lip' image in the scrollview suddenly becomes visible:

```
     <BoxView 
        BindingContext="{x:Reference Name=MaskScrollView}"
        Grid.Row="1" HeightRequest="30" Color="White"
        IsVisible="{Binding ScrollY, 
          Converter={StaticResource MaskConverter},
          ConverterParameter={StaticResource HeroHeightKey}}" Opacity="1"
         />
      <Image 
        BindingContext="{x:Reference Name=MaskScrollView}"
        Grid.Row="1" 
        Source="transparentscrollviewlip.png" Aspect="Fill" HorizontalOptions="StartAndExpand" HeightRequest="30"
        IsVisible="{Binding ScrollY, 
                 Converter={StaticResource MaskConverter},
                 ConverterParameter={StaticResource HeroHeightKey}}" Opacity="1"
             />
```

Notice here that the BoxView and the Image have exactly the same Grid.Row positioning, the same HeightRequest, and the same IsVisible action upon it according to where the scrollview is positioned.

The purpose of the BoxView is to be underneath the stationary 'lip' image, but to provide a background colour beneath the transparency of the image, that is exactly the same colour as the closed view background. Without this BoxView, you would see the items in the ScrollView scrolling upwards beneath it!

###### Quod erat faciendum.

Of course, some might like the Hero Image to not fade out in such a case, however what is plain from the Xaml is that this sort of thing is very easy to do / change / modify - and it's also a great illustration of reverse binding.

The following four images show the action of scrolling, notice that once the scroll-pane comes to it's top position, that the page within it continues to scroll upwards.

![Screenshot 1](https://github.com/Xamtastic/DiminishingMastheadPOC/blob/master/Screenshots/Screen%20Shot%201.png)

![Screenshot 2](https://github.com/Xamtastic/DiminishingMastheadPOC/blob/master/Screenshots/Screen%20Shot%202.png)

![Screenshot 3](https://github.com/Xamtastic/DiminishingMastheadPOC/blob/master/Screenshots/Screen%20Shot%203.png)

![Screenshot 4](https://github.com/Xamtastic/DiminishingMastheadPOC/blob/master/Screenshots/Screen%20Shot%204.png)


