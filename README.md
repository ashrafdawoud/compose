# compose

State hoisting is a pattern in Jetpack Compose that involves moving the state of a composable to its caller, which makes the composable stateless. By hoisting the state, we create a single source of truth, encapsulate the state within the stateful composables that modify it, make it shareable across multiple composables, and make it interceptable by callers to the stateless composables.

The general pattern for state hoisting in Jetpack Compose is to replace the state variable with two parameters: value and onValueChange. The value parameter represents the current value to display, and the onValueChange parameter is a function that requests a change to the value.

Note that you are not limited to onValueChange. If more specific events are appropriate for the composable, you should define them using lambdas like onExpand and onCollapse.

State that is hoisted this way has some important properties:

    Single source of truth: By moving state instead of duplicating it, we're ensuring there's only one source of truth. This helps avoid bugs.
    Encapsulated: Only stateful composables can modify their state. It's completely internal.
    Shareable: Hoisted state can be shared with multiple composables.
    Interceptable: Callers to the stateless composables can decide to ignore or modify events before changing the state.
    Decoupled: The state for the stateless composable may be stored anywhere, making it possible to move it into a ViewModel.

In practice, we can extract the state and the onValueChange functions out of a composable and move them up the tree to a caller composable that passes them as parameters to the child composable. For example, let's say we have a HelloContent composable that displays a greeting with a name and an OutlinedTextField where the user can enter their name. We can hoist the name state and the onNameChange function out of the HelloContent composable and move them up to a HelloScreen composable that calls HelloContent:
kotlin

@Composable
fun HelloScreen() {
    var name by remember { mutableStateOf("") }

    HelloContent(name = name, onNameChange = { name = it })
}

@Composable
fun HelloContent(name: String, onNameChange: (String) -> Unit) {
    Column(modifier = Modifier.padding(16.dp)) {
        Text(text = "Hello, $name")
        OutlinedTextField(value = name, onValueChange = onNameChange, label = { Text("Name") })
    }
}

With this implementation, we can reuse the HelloContent composable in different parts of our UI, and manage the state of the name in a single place. We can also intercept or modify the name change events in the HelloScreen composable before they're used to update the name, and store the name state anywhere we want, such as in a ViewModel.

In summary, state hoisting is a powerful pattern in Jetpack Compose that can help simplify your code, reduce bugs, and make your composables more flexible and reusable.
