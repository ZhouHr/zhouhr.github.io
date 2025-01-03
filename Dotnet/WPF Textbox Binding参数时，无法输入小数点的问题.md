### 使用扩展属性实现

#### 实现扩展属性类

```C#
public class DecimalConverter : IValueConverter
{
	public object Convert(object value, Type targetType, object parameter, 
		System.Globalization.CultureInfo culture)
	{
		return value;
	}

	public object ConvertBack(object value, Type targetType, object parameter, 
		System.Globalization.CultureInfo culture)
	{
		return value.ToString().EndsWith(".") ? "." : value;
	}
}
```

#### 引入到本地资源

```xaml
// 引入命名空间
xmlns:localConverter="clr-namespace:UI.Converter"

// 引入资源类
<UserControl.Resources>
    <localConverter:DecimalConverter x:Key="DecimalConverter" />
</UserControl.Resources>

```

#### 使用实例

```xaml
<TextBox Text="{Binding CurrentWeightList.Price,Mode=TwoWay,UpdateSourceTrigger=PropertyChanged,Converter={StaticResource DecimalConverter}}"
         Width="120" TextAlignment="Center" GotFocus="TextBox_GotFocus"/>
```


### 引用

- [WPF Binding小数，文本框不能输入小数点的问题](https://blog.csdn.net/BeanGo/article/details/127275317)