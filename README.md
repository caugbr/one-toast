# One Toast
### Show toast messages in your Vue app
**One Toast** was designed to show only one message at a time, even if there is a queue of messages to be displayed.

## Vue 3.x
If you are using the version for Vue 3.x, first of all, you must include the line below into your main.js

    import { createApp } from  'vue'
    import  App  from  './App.vue'
    
    const  app = createApp(App);
    
    // One Toast global object
    app.config.globalProperties.$oneToast = {}; // << Insert this line
    
    app.mount('#app');

## Using
Just save it in `src/components` and import **One Toast** (in a place where it is always available) and start to display toast messages from any component, using the global variable `this.$oneToast`, that will be available after **One Toast** starts, regardless of where it was imported. One Toast should be inserted in one single place, but it can be used from any component.

    <template>
        <my-component>
            <one-toast />
        </my-component>
    </template>

	<script>
    import OneToast from '@/components/OneToast';
    export default {
        name: 'MyComponent',
        components: {
            OneToast
        },
        mounted() {
            this.$oneToast.add({
	            type:  'info',
	            title:  'System message',
	            message:  'Message content goes here',
	            autoClose:  20000
            });
        }
    }
    </script>
    

## Configuration
There are some global values and some values that you can define directly in the message object. These ones will be valid only for that message. The global configuration is done trough the component properties, while the message options are set for each message object. 

### Component properties
| Name | Description | Type | Default value | Required |
| -- | -- | -- | -- | -- |
| `positionX` | X axis position ('left', 'center' or 'right') | String | 'left' | Yes |
| `positionY` | Y axis position ('top', 'center' or 'bottom') | String | 'bottom' | Yes |
| `useIcon` | Use default icons? | Boolean | true | Yes |
| `transition` | Transition name ('slide-fade' is built-in) | String | 'slide-fade' | Yes |
| `duration` | Transition duration (milliseconds) | Number | 350 | Yes |
| `showCounterBadge` | Display the counter badge? | Boolean | true | Yes |
| `progressBar` | Display the progress bar? | Boolean | true | Yes |
| `backdrop` | Block page? (you can send an object to replace the backdrop defaults) | Boolean, Object | false | No |
| `closeOnBluur` | Close on backdrop click? | Boolean | false | No |

### Message object
| Name | Description | Type | Default value | Required |
| -- | -- | -- | -- | -- |
| `title` | An optional title for the message | String | '' | No* |
| `message` | The message itself | String | '' | No* |
| `type` | The message type ('success', 'info', 'warn', 'danger' and 'blank') | String | 'success' | Yes |
| `autoClose` | A timeout to close the message (zero to disable) | Number | 0 | No |
| `customClass` | A custom css class | String | '' | No |
| `icon` | An optional custom icon | Object | undefined | No |
| `backdrop` | Block page? | Boolean | false | No |
| `closeOnBluur` | Close on backdrop click? | Boolean | false | No |

\* you must send `title` or `message`


