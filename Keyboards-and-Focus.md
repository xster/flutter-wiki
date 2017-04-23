This page documents our intent with respect to showing system IME affordances (on-screen keyboards) for text fields.

Whenever a focus node requests focus (e.g., because the user tapped the associated text field, the associated text field is first displayed and is autofocused, or the developer explicitly requests to focus the focus node), the focus node gains a *system IME token* if it doesn't already have one.

Whenever a focus node actually receives focus, if the focus node has a *system IME token*, the *system IME token* is consumed and the focus node requests the system display its IME affordance.
