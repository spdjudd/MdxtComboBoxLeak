# MdxtComboBoxLeak

Example app to show memory leak caused by ComboBoxPopup.

To demostrate:

1. Build the app, then start the app in windbg.exe
2. Click the button to create a new view with a ComboBox
3. Close the view
4. Pause windbg.exe, and find the ComboBox instances and their GC roots (e.g. by running these commands:
  1. .cordll -ve -u -l
  2. !DumpHeap -type System.Windows.Controls.ComboBox
  3. !GCRoot [address of instance from above]

Example output:

```
0:019> !GCRoot 02c42f50 
HandleTable:
    00e713e4 (pinned handle)
    -> 03b794c8 System.Object[]
    -> 02da2074 System.Collections.Generic.Dictionary`2[[System.ComponentModel.PropertyDescriptor, System],[MS.Internal.ComponentModel.DependencyObjectPropertyDescriptor, WindowsBase]]
    -> 02e02e08 System.Collections.Generic.Dictionary`2+Entry[[System.ComponentModel.PropertyDescriptor, System],[MS.Internal.ComponentModel.DependencyObjectPropertyDescriptor, WindowsBase]][]
    -> 02dffccc MS.Internal.ComponentModel.DependencyObjectPropertyDescriptor
    -> 02e040bc System.Collections.Generic.Dictionary`2[[System.Windows.DependencyObject, WindowsBase],[MS.Internal.ComponentModel.PropertyChangeTracker, WindowsBase]]
    -> 02e041b0 System.Collections.Generic.Dictionary`2+Entry[[System.Windows.DependencyObject, WindowsBase],[MS.Internal.ComponentModel.PropertyChangeTracker, WindowsBase]][]
    -> 02da0ab8 MaterialDesignThemes.Wpf.ComboBoxPopup
    -> 02c42f50 System.Windows.Controls.ComboBox

Found 1 unique roots (run '!GCRoot -all' to see all roots).
```

The DependencyObjectPropertyDescriptor at 02dffccc is for the ComboBoxPopup.ChildProperty
