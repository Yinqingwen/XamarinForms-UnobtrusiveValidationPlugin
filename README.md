# Xamarin.Forms Unobtrusive FluentValidation Plugin
A plugin library that integrates the functionality of FluentValidation and Xamarin.Forms to provide an unobtrusive user experience.


The library follows an MVVM pattern and below are examples on how to get started.  
 
 ## Preview
 ![alt text](https://github.com/james-russo/XamarinForms-UnobtrusiveValidationPlugin/blob/master/samples/sample.gif "Sample In Action")

 
 ### Get Started
 
 #### FluentValidation AbstractValidator (EnhancedAbstractValidator)
 
 When using FluentValidation library, normally you would derive from *AbstractValidator<T>*, however in this library you need to use **EnhancedAbstractValidator<T>**.  
 This class extends AbstractValidator<T> on and provides a new method **RuleForProp**.  This method is essential in order for your view model later to work with the validator.
 
 ```csharp
 
 public class PersonValidator : EnhancedAbstractValidator<PersonViewModel>
 {
	public PersonValidator()
	{
		RuleForProp(a => a.FirstName)
			.NotNull();
	} 
 }
 
 ```
 
 ### ViewModel (AbstractValidationViewModel)
 
 When deriving from AbstractValidationViewModel, you will want to the class **ValidatableProperty<>**.  This property encapsulates the needed propertys for the UI to bind to accordingly.
 
 ```csharp
 
 [FluentValidation.Attributes.Validator(typeof(PersonValidator))]
 public class PersonViewModel : AbstractValidationViewModel
 {
	public ValidatableProperty<string> FirstName { get; set; } = new ValidatableProperty<string>();
 }
 
 ```
 
 ### Xamarin.Forms UI Page
 
 #### Code Page
 ```csharp
    public class PersonPage : ContentPage
    {
        public PersonPage()
        {
            BindingContext = new PersonViewModel();

			//Here we pass in the name of the property we would like this control to bind to. 
            var firstNameControl = new ValidatableEntryControl(nameof(PersonViewModel.FirstName));

            var sl = new StackLayout()
            {
                Orientation = StackOrientation.Vertical
            };

            sl.Children.Add(firstNameControl);
			
            var submitButton = new Button()
			{
				Text = "Submit"
			};

			//Sample to trigger validator, normally you would call the validator from within your view model.
            submitButton.SetBinding(Button.CommandProperty, nameof(PersonViewModel.ValidateCommand));

            sl.Children.Add(submitButton);

            this.Content = sl;
        }

    }
 
 ```
 #### XAML Page
 ```xml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" 
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" 
    x:Class="Test.XamlTest"
    xmlns:custom="clr-namespace:Xamarin.Plugins.UnobtrusiveFluentValidation;assembly=Xamarin.Plugins.FluentValidation">
	<ContentPage.Content>
        <StackLayout Orientation="Vertical">
            <Label>Name</Label>
            <custom:ValidatableEntryControl BindingName="Name"></custom:ValidatableEntryControl>
            <Button x:Name="btnSubmit" Text="Submit" Command="{Binding ValidateCommand}"></Button>
        </StackLayout>
	</ContentPage.Content>
</ContentPage>	

 ```
