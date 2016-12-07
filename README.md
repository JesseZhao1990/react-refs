## Refs and the DOM

在一般的react数据通信中，props是父组件和子组件交互的唯一方式。原理就是通过使用新的props来重新渲染子组件。然而，有几种特殊的情况
In the typical React dataflow, props are the only way that parent components interact with their children. To modify a child, you re-render it with new props. However, there are a few cases where you need to imperatively modify a child outside of the typical dataflow. The child to be modified could be an instance of a React component, or it could be a DOM element. For both of these cases, React provides an escape hatch.

### The ref Callback Attribute
React supports a special attribute that you can attach to any component. The ref attribute takes a callback function, and the callback will be executed immediately after the component is mounted or unmounted.

When the ref attribute is used on an HTML element, the ref callback receives the underlying DOM element as its argument. For example, this code uses the ref callback to store a reference to a DOM node:

    
    class CustomTextInput extends React.Component {
      constructor(props) {
        super(props);
        this.focus = this.focus.bind(this);
      }

      focus() {
        // Explicitly focus the text input using the raw DOM API
        this.textInput.focus();
      }

      render() {
        // Use the `ref` callback to store a reference to the text input DOM
        // element in this.textInput.
        return (
          <div>
            <input
              type="text"
              ref={(input) => { this.textInput = input; }} />
            <input
              type="button"
              value="Focus the text input"
              onClick={this.focus}
            />
          </div>
        );
      }
    }

    
  
React will call the ref callback with the DOM element when the component mounts, and call it with null when it unmounts.

Using the ref callback just to set a property on the class is a common pattern for accessing DOM elements. If you are currently using this.refs.myRefName to access refs, we recommend using this pattern instead.

When the ref attribute is used on a custom component, the ref callback receives the mounted instance of the component as its argument. For example, if we wanted to wrap the CustomTextInput above to simulate it being clicked immediately after mounting:

      class AutoFocusTextInput extends React.Component {
        componentDidMount() {
          this.textInput.focus();
        }

        render() {
          return (
            <CustomTextInput
              ref={(input) => { this.textInput = input; }} />
          );
        }
      }

You may not use the ref attribute on functional components because they don't have instances. You can, however, use the ref attribute inside the render function of a functional component:

      function CustomTextInput(props) {
        // textInput must be declared here so the ref callback can refer to it
        let textInput = null;

        function handleClick() {
          textInput.focus();
        }

        return (
          <div>
            <input
              type="text"
              ref={(input) => { textInput = input; }} />
            <input
              type="button"
              value="Focus the text input"
              onClick={handleClick}
            />
          </div>
        );  
      }

### Don't Overuse Refs
Your first inclination may be to use refs to "make things happen" in your app. If this is the case, take a moment and think more critically about where state should be owned in the component hierarchy. Often, it becomes clear that the proper place to "own" that state is at a higher level in the hierarchy. See the Lifting State Up guide for examples of this.
