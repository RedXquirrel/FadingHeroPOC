# Fading Hero POC
In this Proof-of-Concept, scrolling the ScrollView reduces the height of the hero pane, as well as fading out some of the hero pane content (White label text, image), and fading in some new content (Black label text). Once the hero is at it's minimum height, the scroll-pane continues to scroll under it. Note that before fading, the hero title is uppercase, but after scrolling, it is lowercase.

Done in Xaml with two IValueConverters to manage opacity and hero height changes as bound to the ScrollY value of the ScrollView.

It all starts with a ScrollView, vis-a-vis:

```
    <ScrollView x:Name="MaskScrollView" Grid.Row="1" Grid.RowSpan="4">
      <StackLayout StackLayout.Spacing="0">
        <BoxView HeightRequest="{StaticResource HeroHeightKey}" Color="Transparent" />
        <Image Source="transparentscrollviewlip.png" Aspect="Fill" HorizontalOptions="StartAndExpand" HeightRequest="30" />
        ...
```

Notice the BoxView, which is transparent - this affords the underlying hero image to be seen!

```
        <BoxView HeightRequest="{StaticResource HeroHeightKey}" Color="Transparent" />
```

You will also notice that under thge BoxView is an image that has a transparent top, this is the 'lip' of the elements that scroll upwards, and the reason why this image has a transparency at the top (diagnol, in this case), is to give a cue to the user when it is closed, that they should swipe doen to reveal the hidden content.

```
<Image Source="transparentscrollviewlip.png" ...
```


Notice, in particular, the HeroHeightKey that is used as the BoxView's HeightRequest - this value is needed by a few ui elements to coordinate the visual effect, hence this key exists for each of the coordinating ui elements to reference in the page's ResourceDictionary ... just change the value of this key and the height of the transparent BoxView is changed, and all of the coordinating elements follow suit:

```
    <ResourceDictionary>

      <x:Double x:Key="HeroHeightKey">275</x:Double>
      
      <converters:MaskConverter x:Key="MaskConverter" />
      
      <converters:ScrollViewingFadingOpacityConverter x:Key="ScrollViewingFadingOpacityConverter" />
      <converters:ScrollViewingUnfadingOpacityConverter x:Key="ScrollViewingUnfadingOpacityConverter" />
      
    </ResourceDictionary>
```

This is used in many places that reference the ScrollY value of the ScrollView, and here you can see the value passing through to the IValueConverter that is used by the Hero image to fade:

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

Reverse Binding (that is, the references that are made to the ScvrollView's ScrollY property are made by setting the BindingContext of these elements to the name of the ScrollView itself:

```
BindingContext="{x:Reference Name=MaskScrollView}"
```

In this way, the Opacity of the fading elements can be set to the ScrollView's ScrollY property, and this value returned by the converter to be between 0 and 1.

Of course, some might like the Hero Image to not fade out in such a case, however what is plain from the Xaml is that this sort of thing is very easy to do / change / modify - and it's also a great illustration of reverse binding.

The following four images show the action of scrolling, notice that once the scroll-pane comes to it's top position, that the page within it continues to scroll upwards.

![Screenshot 1](https://github.com/Xamtastic/DiminishingMastheadPOC/blob/master/Screenshots/Screen%20Shot%201.png)

![Screenshot 2](https://github.com/Xamtastic/DiminishingMastheadPOC/blob/master/Screenshots/Screen%20Shot%202.png)

![Screenshot 3](https://github.com/Xamtastic/DiminishingMastheadPOC/blob/master/Screenshots/Screen%20Shot%203.png)

![Screenshot 4](https://github.com/Xamtastic/DiminishingMastheadPOC/blob/master/Screenshots/Screen%20Shot%204.png)


