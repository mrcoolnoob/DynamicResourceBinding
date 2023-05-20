## Question

In WPF, I have a label whose background is set to a dynamic resource.
It is working fine such that when we change the value of the resource which in this case the color it does work.
However, in between we're setting the background of the same label from the code behind which also does work but after that when the dynamic resource value is changed it doesn't work.

## Solution

Yes, I think I understand your situation.

You have a Label control in WPF where the background color is set to a dynamic resource. Initially, this works as expected. But, when you programmatically change the background color from the code behind, it appears to break the dynamic resource binding, and any subsequent changes to the dynamic resource aren't reflected in the Label.

This behavior occurs because when you set the background color programmatically in the code behind, you're essentially overriding the previous dynamic resource binding. In WPF, local values (values directly set on the element) take precedence over resource bindings, according to the dependency property value precedence system. That's why, after setting a local value, the dynamic resource doesn't appear to work anymore.

If you want to change the color of the label temporarily and then have it continue to respect the dynamic resource, you could:

Save the current dynamic resource binding before changing the background color,
Apply your temporary color change, and then
Re-apply the dynamic resource binding when you're done.

```c#
// Save the existing dynamic resource binding.
var existingBackgroundBinding = BindingOperations.GetBinding(myLabel, Control.BackgroundProperty);

// Change the background color.
myLabel.Background = new SolidColorBrush(Colors.Red);

// Some time later, reapply the dynamic resource binding.
BindingOperations.SetBinding(myLabel, Control.BackgroundProperty, existingBackgroundBinding);
```

This way, you're storing the original dynamic resource binding before changing the color, and then restoring it after you're done. This ensures the Label will continue to respect changes to the dynamic resource.

However, this approach assumes that the Background property of your Label control is bound to the dynamic resource through a Binding. If it's not, and the dynamic resource is set in XAML, you may need to retrieve the resource key and reapply the dynamic resource this way:

```c#
// Save the existing dynamic resource key.
var existingBackgroundResourceKey = myLabel.Background as DynamicResourceExtension;

// Change the background color.
myLabel.Background = new SolidColorBrush(Colors.Red);

// Some time later, reapply the dynamic resource.
myLabel.SetResourceReference(Control.BackgroundProperty, existingBackgroundResourceKey.ResourceKey);
```

These are just rough examples. You'll need to adapt them to your specific context.




