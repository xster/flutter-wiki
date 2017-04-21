This page documents our intent with respect to showing system IME affordances (on-screen keyboards) for text fields.

System IME affordances should be displayed for a text field in the following cases:

* When the user taps the field.

* When the text field is first displayed, if the text field is autofocused.

* When the API for focusing-and-showing-the-keyboard is called.

System IME affordances should not be displayed for a text field in any case not covered above, including:

* After the user has dismissed the IME.

* When a previously-focused field regains the focus due to a focus scope (e.g. dialog) going away, if the IME was not visible while that focus scope was up.

* When a text field first appears, if the text field is not autofocused.

* When the text field is focused, unless it was via one of the mechanisms described above.