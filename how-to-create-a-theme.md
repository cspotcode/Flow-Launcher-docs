## Overview

If you're making a theme for the first time, start from an existing one: copy **sublime.xaml** (one of the basic theme files) in Flow's theme folder to a new file, or download [Sublime.xaml](https://github.com/Flow-Launcher/Flow.Launcher/blob/dev/Flow.Launcher/Themes/Sublime.xaml).

## ⛔ Caution ⛔

Place your theme in the Themes folder inside the UserData directory. For a roaming install this is `%APPDATA%\FlowLauncher\Themes\` (AppData Roaming path); for a portable install it is `%localappdata%\FlowLauncher\app-<VersionOfYourFlowLauncher>\UserData\Themes\` by default (AppData Local path). Flow reads custom themes from the UserData directory and default themes from its own app directory. Don't place your theme outside UserData: it would be erased along with the default theme files on the next update.

## Theme elements

The theme file sets the following parts. Each style has a key and properties you can modify. If a theme has a key not described here, we recommend not modifying it.

(*This section may change or be removed in future versions.*)

![Flow Launcher screenshot](https://cdn.jsdelivr.net/gh/Flow-Launcher/docs@main/assets/themelayout.png)

### WindowBorderStyle

Sets the color, border size, border color, and corner radius of the main window.

```xml
<Style x:Key="WindowBorderStyle" BasedOn="{StaticResource BaseWindowBorderStyle}" TargetType="{x:Type Border}">
        <Setter Property="BorderThickness" Value="2" />
        <Setter Property="BorderBrush" Value="#6c7279" /> 
        <Setter Property="CornerRadius" Value="5" />
        <Setter Property="Background" Value="#303840" />
 </Style>
```

Recommended: a border thickness of 1 or 2, and a corner radius of 5 or less (0 is fine).

<br>

### QueryBoxStyle

The style of the search window's input: font size, cursor color, font color, and input height. Reducing the font size also reduces the window height, so specify the height.

```xml
<Style x:Key="QueryBoxStyle" BasedOn="{StaticResource BaseQueryBoxStyle}" TargetType="{x:Type TextBox}">
        <Setter Property="Background" Value="#303840" />  <!-- Set it to the same color as the window. -->
        <Setter Property="Foreground" Value="#d2d8e5" /> <!-- Font Color -->
        <Setter Property="CaretBrush" Value="#FFAA47" /> <!-- Cursor Color -->
        <Setter Property="FontSize" Value="26" />
        <Setter Property="Height" Value="42" /> <!-- Default is 42. -->
</Style>
``` 

<br>

### QuerySuggestionBoxStyle

The style of the suggested search word that appears after the search text. Use the same font size and height as QueryBoxStyle, and preferably a more translucent color.

```xml
<Style x:Key="QuerySuggestionBoxStyle" BasedOn="{StaticResource BaseQuerySuggestionBoxStyle}" TargetType="{x:Type TextBox}">
        <Setter Property="Background" Value="#303840" />
        <Setter Property="Foreground" Value="#798189" /> <!-- Font Color -->
        <Setter Property="FontSize" Value="26" /> <!-- Same as QueryBox -->
        <Setter Property="Height" Value="42" /> <!-- Same as QueryBox -->
</Style>
```

<br>

### PendingLineStyle

Sets the color of the loading bar that sometimes appears.

```xml
<Style x:Key="PendingLineStyle" BasedOn="{StaticResource BasePendingLineStyle}" TargetType="{x:Type Line}">
        <Setter Property="Stroke" Value="#FFAA47" /> <!-- Bar Color -->
</Style>
```

<br>

### SearchIconStyle

The style of the magnifying glass icon on the right side of the search window. Its color and size can be changed, or it can be hidden. (The picture will be updated later.)

```xml
<Style x:Key="SearchIconStyle" TargetType="{x:Type Path}" BasedOn="{StaticResource BaseSearchIconStyle}">
        <Setter Property="Fill" Value="#3c454e" /> <!-- Color -->
        <Setter Property="Width" Value="32" /> <!-- Size. Default is 32. -->
        <Setter Property="Height" Value="32" /> <!-- Size -->
</Style>
```

To hide it, add:

```xml
<Setter Property="Visibility" Value="Collapsed" />
```

<br>

### ItemTitleStyle

The title of a search result. Sets its font size and color.

```xml
<Style x:Key="ItemTitleStyle"  BasedOn="{StaticResource BaseItemTitleStyle}" TargetType="{x:Type TextBlock}">
	<Setter Property="Foreground" Value="#5989b2" /> 
	<Setter Property="FontSize" Value="13" /> <!-- Default is 16 -->
</Style>
```

<br>

### ItemTitleSelectedStyle

The color used when the item is focused. Keep the font size the same as ItemTitleStyle.

```xml
<Style x:Key="ItemTitleSelectedStyle" BasedOn="{StaticResource BaseItemTitleSelectedStyle}"  TargetType="{x:Type TextBlock}" >
        <Setter Property="Foreground" Value="#5bafb0" />
</Style>
```

<br>

### ItemSubTitleStyle

The file path of a search result. Sets its font size and color.

```xml
<Style x:Key="ItemSubTitleStyle" BasedOn="{StaticResource BaseItemSubTitleStyle}" TargetType="{x:Type TextBlock}" >
        <Setter Property="Foreground" Value="#7b858f" />
        <Setter Property="FontSize" Value="13" /> <!-- Default is 13 -->
</Style>
```

<br>

### ItemSubTitleSelectedStyle

The color used when the item is focused. Keep the font size the same as ItemSubTitleStyle.

```xml
<Style x:Key="ItemSubTitleSelectedStyle" BasedOn="{StaticResource BaseItemSubTitleSelectedStyle}" TargetType="{x:Type TextBlock}" >
        <Setter Property="Cursor" Value="Arrow" />
        <Setter Property="Foreground" Value="#cc8ec8" />
</Style>
```    

<br>

### ItemHotkeyStyle

Specifies the color and size of the Hotkey font.

```xml
<Style x:Key="ItemHotkeyStyle" TargetType="{x:Type TextBlock}">
        <Setter Property="FontSize" Value="13" />
        <Setter Property="Foreground" Value="#5bafb0" />
</Style>
```

<br>

### ItemHotkeySelectedStyle

The color used when the item is focused. Keep the font size the same as `ItemHotkeyStyle`.

```xml
<Style x:Key="ItemHotkeySelectedStyle" TargetType="{x:Type TextBlock}">
        <Setter Property="FontSize" Value="13" />
        <Setter Property="Foreground" Value="#ea7354" />
</Style>
```    

<br>

### ItemSelectedBackgroundColor

The background color of the selected item.

```xml
<SolidColorBrush x:Key="ItemSelectedBackgroundColor">#3c454e</SolidColorBrush>
```

<br>

### HighlightStyle

Highlights the part of a result that matches the search text. Sets the color and font weight.

```xml
<Style x:Key="HighlightStyle">
        <Setter Property="Inline.Foreground" Value="#ea7354" />
        <Setter Property="Inline.FontWeight" Value="Bold" />
</Style>
```    

<br>

### ThumbStyle

Specifies the color and size of the scroll bar.

```xml
<Style x:Key="ThumbStyle" BasedOn="{StaticResource BaseThumbStyle}" TargetType="{x:Type Thumb}">
        <Setter Property="SnapsToDevicePixels" Value="True"/>
        <Setter Property="OverridesDefaultStyle" Value="true"/>
        <Setter Property="IsTabStop" Value="false"/>
        <Setter Property="Width" Value="2"/> <!-- Size of Thumb -->
        <Setter Property="Focusable" Value="false"/>
        <Setter Property="Template">
            <Setter.Value>
                <ControlTemplate TargetType="{x:Type Thumb}">
                    <Border CornerRadius="2" DockPanel.Dock="Right" Background="#6c7279" BorderBrush="Transparent" BorderThickness="0" /> <!-- Background is Color of Thumb -->
                </ControlTemplate>
            </Setter.Value>
        </Setter>
    </Style>
```    

<br>

### SeparatorStyle

Sets the size, height, color, and margin of the horizontal line. You can hide it if you don't need it.

```xml
<Style x:Key="SeparatorStyle" BasedOn="{StaticResource BaseSeparatorStyle}" TargetType="{x:Type Rectangle}">
        <Setter Property="Fill" Value="#3c454e"/>
        <Setter Property="Height" Value="1"/> 
        <Setter Property="Margin" Value="0 0 0 8"/> <!--  It is left, up, right, and down in order. -->
</Style>
```    

To hide it, add:

```xml
<Setter Property="Visibility" Value="Collapsed" />
```

<br>

### ItemGlyph

Specifies the color of the glyph icon.

```xml
<Style x:Key="ItemGlyph"  BasedOn="{StaticResource BaseGlyphStyle}" TargetType="{x:Type TextBlock}">
        <Setter Property="Foreground" Value="#5bafb0" />
</Style>
```   

----
### Theme Info
You can add the following theme information at the top of the file: the theme name, whether blur is supported, and whether dark mode is supported. Flow's theme list shows these as icons.
```xml
<!--
    Name: Windows 11
    IsDark: True
    HasBlur: True
-->
```

## How to Make Blur theme
Add the following values to the theme file.

```xml
    <system:Boolean x:Key="ThemeBlurEnabled">True</system:Boolean>
    <system:String x:Key="SystemBG">Auto</system:String>
    <Color x:Key="LightBG">#BFFAFAFA</Color>
    <Color x:Key="DarkBG">#DD202020</Color>
```
Set `ThemeBlurEnabled` to `True` for themes that support blur, and False otherwise.
`SystemBG` selects the default window background color drawn by the system when blur is enabled. It can be set to `Light`, `Dark`, or `Auto`.
`Auto` automatically switches between `Light` and `Dark` based on Flow's ColorScheme setting.
`LightBG` is the window color used in `Light` mode, and `DarkBG` is the window color used in `Dark` mode.

When the blur effect is enabled, users can't disable window shadows, and the system determines the window corner radius. Any values the theme sets for these properties are ignored.

### Blur Effects

Windows 11 users can choose from four blur effects:


#### **None**

No blur effect is applied. The window background color is determined in the following order of priority:

1. The `LightBG` or `DarkBG` color with the alpha (transparency) value removed.  
2. If `LightBG`/`DarkBG` is not set, the background color from `WindowBorderStyle` is used.


#### **Acrylic**

1. The system-defined Light/Dark background is applied first.  
2. Then, the colors defined in `LightBG` and `DarkBG` are drawn on top, like a tint.
3. The `LightBG` and `DarkBG` values should include some level of alpha (transparency), and the colors should not differ too drastically from the system-applied base color.

In other words, avoid background colors that differ too much in tone from the default window colors Windows draws.

#### **Mica / Mica Alt**

- The user-defined `LightBG` and `DarkBG` are completely ignored.  
- The background color is automatically determined based on the user's desktop wallpaper.  
- It uses either a Light or Dark variant, selected according to the `SystemBG` setting.

#### 💡 Design Recommendation

Because the window background can change significantly with the user's blur settings, **design Blur themes by adjusting the opacity of black or white text and elements**, to ensure good contrast and readability.
<br>

----

## How to Make Auto Dark Mode theme
- If the SystemBG property is set to Auto and both LightBG and DarkBG are specified, the theme switches automatically.
- Base the Light and Dark mode colors of control elements on Flow's resource definitions, so they switch automatically with the theme. (These can't currently be defined within the theme itself.)
Use the built-in Windows11 theme as a reference, and see these links:

- https://github.com/Flow-Launcher/Flow.Launcher/blob/dev/Flow.Launcher/Themes/Win11Light.xaml
- https://github.com/Flow-Launcher/Flow.Launcher/blob/dev/Flow.Launcher/Resources/Light.xaml
- https://github.com/Flow-Launcher/Flow.Launcher/blob/dev/Flow.Launcher/Resources/Dark.xaml
----

## Let's share it
Once you've crafted your perfect theme, share it with the community:

[Theme Gallery](https://github.com/Flow-Launcher/Flow.Launcher/discussions/1438)
