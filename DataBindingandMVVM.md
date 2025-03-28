# Data Binding in .NET MAUI

## What is Data Binding?
Data binding connects UI elements (Views) with data (ViewModel), eliminating the need for manual updates when data changes.

## Types of Data Binding in .NET MAUI

### One-Way Binding
Data flows from **ViewModel → View**.

### Two-Way Binding
Data flows both ways, updating both the UI and ViewModel.

### One-Time Binding
Data is set once and does not change even if the source updates.

### Default Binding
Each control has a default binding mode.

### OneWayToSource Binding
Read-only bindable properties default to **OneWayToSource**, where data flows from **View → ViewModel**. Example: `SelectedItem` in `ListView` updates the binding source when the selection changes.

## String Formatting in Data Binding
String formatting in .NET MAUI data binding allows formatting bound values directly in XAML using the `StringFormat` property.

**Example:**
```xml
<Label Text="{Binding Price, StringFormat='Price: {0:C}'}" />
```
This formats the `Price` property as a currency value.

## Bindings with a Binding Context
The `BindingContext` defines the data source for bindings in a view. Setting the `BindingContext` allows UI elements to access properties in the ViewModel.

**Example:**
```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
        BindingContext = new MainViewModel();
    }
}
```
Here, `MainViewModel` is set as the `BindingContext`, making its properties available for data binding in XAML.

## Bindings in XAML vs. C# (SetBinding)

### XAML Binding Example:
```xml
<Label Text="{Binding Name}" />
```
This binds the `Text` property of the `Label` to the `Name` property of the ViewModel.

### C# SetBinding Example:
```csharp
var label = new Label();
label.SetBinding(Label.TextProperty, "Name");

## MVVM Pattern in .NET MAUI
MVVM (Model-View-ViewModel) is an architectural pattern that separates UI logic from business logic.

### MVVM Components
- **Model (M):** Represents the data and business logic (e.g., database models).
- **View (V):** Represents the UI (XAML).
- **ViewModel (VM):** Acts as a bridge between Model and View, containing properties and commands.

### MVVM Benefits:
✅ Better separation of concerns  
✅ Easier unit testing  
✅ Code reusability  

---

# Simple MVVM Example in .NET MAUI (Without NuGet)

## 1. Model (Person.cs)
```csharp
public class Person
{
    public string Name { get; set; }
}
```
---

## 2. ViewModel (MainViewModel.cs)
```csharp
using System.ComponentModel;
using System.Runtime.CompilerServices;

public class MainViewModel : INotifyPropertyChanged
{
    private string _name;
    
    public string Name
    {
        get => _name;
        set
        {
            _name = value;
            OnPropertyChanged();
        }
    }

    public ICommand ChangeNameCommand { get; }

    public MainViewModel()
    {
        Name = "John Doe";
        ChangeNameCommand = new Command(ChangeName);
    }

    private void ChangeName()
    {
        Name = "Jane Doe";
    }

    public event PropertyChangedEventHandler PropertyChanged;
    protected void OnPropertyChanged([CallerMemberName] string propertyName = "")
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```
---

## 3. View (MainPage.xaml)
```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MVVMExample.MainPage">
    <VerticalStackLayout Padding="20">
        <Entry Text="{Binding Name}" Placeholder="Enter Name"/>
        <Label Text="{Binding Name}" FontSize="24"/>
        <Button Text="Change Name" Command="{Binding ChangeNameCommand}"/>
    </VerticalStackLayout>
</ContentPage>
```
---

## 4. Set ViewModel in MainPage.xaml.cs
```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
        BindingContext = new MainViewModel();
    }
}
```
---

## How it Works:
- `Entry` is bound to the `Name` property.
- `Label` displays the bound `Name` property.
- Changes in `Entry` update `Label` instantly via MVVM.
- Changes in `Entry` or button click update `Label` instantly via MVVM.

This keeps the architecture clean and follows the MVVM pattern without using any external libraries. 🚀

# Simplified MVVM Example in .NET MAUI (With Microsoft MVVM Toolkit)

## 1. Install NuGet Package
To simplify MVVM, install `CommunityToolkit.Mvvm` NuGet package.

```
Install-Package CommunityToolkit.Mvvm
```

---

## 2. Model (Person.cs)
```csharp
public class Person
{
    public string Name { get; set; }
}
```
---

## 3. ViewModel (MainViewModel.cs)
```csharp
using CommunityToolkit.Mvvm.ComponentModel;
using CommunityToolkit.Mvvm.Input;

public partial class MainViewModel : ObservableObject
{
    [ObservableProperty]
    private string name = "John Doe";

    [RelayCommand]
    private void ChangeName()
    {
        Name = "Jane Doe";
    }
}
```
---

## 4. View (MainPage.xaml)
```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MVVMExample.MainPage">
    <VerticalStackLayout Padding="20">
        <Entry Text="{Binding Name}" Placeholder="Enter Name"/>
        <Label Text="{Binding Name}" FontSize="24"/>
        <Button Text="Change Name" Command="{Binding ChangeNameCommand}"/>
    </VerticalStackLayout>
</ContentPage>
```
---

## 5. Set ViewModel in MainPage.xaml.cs
```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
        BindingContext = new MainViewModel();
    }
}
```
---

## How it Works:
- `ObservableProperty` automatically generates `INotifyPropertyChanged`.
- `RelayCommand` simplifies command creation.
- `Entry`, `Label`, and `Button` work seamlessly with minimal code.
- No need to manually implement `INotifyPropertyChanged`.

This keeps the architecture clean and reduces boilerplate code while following MVVM best practices. 🚀
