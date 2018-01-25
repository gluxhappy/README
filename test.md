When we have:
```java
class Demo {
    public void f(int x){
        a.g(y);
    }
}
```
Where
1. f(x) - is the context method
2. g(x) - is the called method

##Example:
```json
[
    {
        "contextMethodName": "onStart",
        "contextSignature": "V",
        "contextIsInstanceOf": ["Landroid/app/Activity;"],
        "action": "ADD_AND_CREATE_METHOD",
        "startHook": {
            "methodName": "onStart_startHook",
            "methodOwner": "Lcom/hp/rum/mobile/hp4m/application/lifecyclehooks/ActivityHooks;",
            "methodSignature": "VLandroid/content/Context;",
            "arguments": {
                "sendThisPointer": "true"
            }
        },
        "endHook": {
            "methodName": "onStart_endHook",
            "methodOwner": "Lcom/hp/rum/mobile/hp4m/application/lifecyclehooks/ActivityHooks;",
            "methodSignature": "VLandroid/content/Context;",
            "arguments": {
                "sendThisPointer": "true"
            }
        }
    }
]
```
* `String` contextMethodName  
   A regex to match to the name of the context method
* `String` nameFromXml  
   will hold the tag in the Manifest XML which holds the name of the method.
   This will be used only for the onClick call so till we find another use the 
   constant of this field will be ignored
* `String` contextSignature  
   the method description in asmdex/smali format. Will match methods that it can 
   accept the arguments which they accept
* `String` contextIsInstanceOf  
   An array of classes. Instrumentation will happen if the context class extends or  
   implements a class in the array.
* `String` calledMethodName  
   same as contextMethodName, but for the called method
* `String` calledSignature  
   same as contextMethodName, but for the called method
* `String` calledIsInstanceOf  
   An array of classes. Instrumentation will happen if the class  of the extends or 
   implements a class in the array.
* `Enum` action  
   can be one of values below  
| Enum                   | meaning                                                    |  
| ---------------------- | ---------------------------------------------------------- |  
| add                    | will add hooks at the beginning and end of the method.     |  
| add_and_create_method  | will add the method is it doesn't exist in the class and then add hooks        |  
| add_and_create_class   | will add a class, a method and place the hooks.            |  
| replace                | will replace any call to function with startHook.          |  
| replace_classes        | will replace one class with another in all occurences.     |  
| replace_service        | will replace a service in the manifest file with another.  |  
| wrap                   | will add a hook before and after every call to the method. |  
| add_in_catch           | will add hooks at the beginning of each catch statement. The hooks first argument MUST be the exception |  
| add_permission         | will add permissions to the app manifest file.             |  
  
* access  
   **PRIVATE**, **PROTECTED** or **PUBLIC**.   
   used only if action is add_and_create_method the 
   default value is **PROTECTED**
   
* opCodesToExclude
   Array of instructions that when we encounter them we skip the instrumentation.
   for example: Opcode.INSN_INVOKE_SUPER (number 111) means that in case
   the instrumentation suspect involves super invocation don't instrument.
   
* startHook
  * methodName  
     the name of the start hook method or the name of the replacing method
  * contextMethodName  
     the name of the context method we are inside of it, in case of wrap
  * methodOwner  
     the class which this method resides in
  * methodSignature  
     the signature of the start hook method or the signature of the replacing method
  * arguments  
    * contextArgArray  
       an int array containing the arguments from the host method (the method in which the i13n is happening).
       First argument is 0, last (num of args - 1)
    * calledArgArray  
       an array containing the arguments that we want to send from the arguments sent to the called method.
       this will be relevant only for wrap/replace actions
    * sendThisPointer  
       true/false, true will send the "this" pointer to the hook
    * sendThisCaller  
       true/false, true will send the caller object pointer to the hook
* endHook  
   same as startHook
* classReplacement array  
  * oldName  
     old class name in the Landroid/bluetooth/BluetoothAdapter; format
  * newName  
     new class name in the same format
* permissions array  
   permission-i.e. android.permission.INTERNET",...
* serviceReplacement array  
  * findByPropertyName  
     property used to locate the service to replace, i.e. permission
  * findByPropertyValue  
     desired property value, i.e. android.permission.BIND_NFC_SERVICE
  * newName  
     replacing service, like com.hpe.sv.android.nfc.SvHostApduServiceWrapper
  * oldNamePropertyName  
     name of the metadata property to store the old service name
