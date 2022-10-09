* toc
{:toc}

# React Hooks
Hooks 在react 16.8中开始引入。目的是在使用state和其他react功能时，不需要写class。

### Class Component vs. Functional Component

## WHY
### Why do we introduce hooks?
*   在components之间复用有状态的逻辑很困难  
    可以通过hooks将逻辑抽出来，并可以独立的测试和重用
*   复杂的component很难理解  
    hooks可以将一个component拆成更小的方法，基于逻辑相关性，而不是生命周期
*   Classes 让人和机器都很困惑
## WHAT
### What are they?
#### Basic Hooks
*   useState  
    *   作用  
    可以将React state添加到方法组件中。
        ```js
        const [state, setState] = useState(initialState)
        ```
        该方法返回一个有状态的值和一个可以更新值的方法。  

    *   注意事项
        *   **useState**不同于class component 中的**setState**，不会自动合并更新的对象。  
        *   初始状态用于首次render，在随后的render中，是不再使用的。我们也可以传入一个方法，来做到延迟初始化的目的  
        ```js
        const [state, setState] = useState(() => {
          const initialState = someExpensiveComputation(props);
          return initialState;
        })
        ```  

        *   如果update的值和之前一样，（使用Object.is()来判断，类似与‘===’，差异在于signed zeros and NaNs）react不会render或者firing effects。如果在render的过程中，有耗时的计算，那么可以考虑使用**useMemo**来优化。  


*   useEffect  
    *   作用
        *   让我们可以在方法组件中，执行副作用。
        *   告诉React在render之后要执行什么effect。
        *   哪些行为是副作用
            *   数据获取
            *   设置监听
            *   手动操作DOM
            *   设置定时器
    *   注意点
        *   useEffect类似于类组件中的**componentDidMount**、**componentDidUpdate**、**componentWillUnmount**的组合。
        *   默认情况下，每次render后，并且保证在下一次render前，都会被调用。每个effect对应每一次re-render，可以保证数据是有效的。同时避免类组件中因为确实更新逻辑二导致的常见bug。
        *   useEffect是异步的，不会block浏览器内容的更新，这会让app看起来反应更快。在某些不常见的case中，比如要计算布局，可以使用另外一个hook，**useLayoutEffect**，用来处理同步的操作。
        *   使用多个useEffect来分离关注点，根据功能来分割代码。
        *   可以通过传入依赖数组来优化不必要的调用，如果只需要在mount时调用，可以传入一个空数组，表明不依赖任何props或者state。
*   useContext  
    *   作用  
        Context提供一种简化多层级传输数据的方式，不需要每一层都手动传。适用于某些“全局”数据，需要被不同层级的组件共用时。  
        useContext  让我们可以在方法组件中获取到context
    *   注意点  
        如果仅仅是为了传递数据，可以考虑组合component。

#### Additional Hooks
*   useReducer  
    类似于Redux，可以作为useState的替代方案，当state的逻辑比较复杂，包含多个子值，或者后一个状态依赖前一个状态。
*   useCallback  
    返回一个memoized callback，并且仅当依赖列表发生变化时，才会跟着变化。可以用于优化不必要的component刷新。
*   useMemo  
    返回一个memoized 值，并且仅当依赖列表发生变化时重新计算。目的在于避免每次render时的繁重耗时的计算。memo的方法中，不可以加入side effects。
*   useRef  
    返回一个可变的ref对象，并通过.current属性获取最新的值，其本身在component的整个生命周期中保持不变。需要注意的是，current的修改并不会导致重新render，如果有这样的需要，可以考虑callback ref。
*   useImperativeHandle  
    配合ref使用。
*   useLayoutEffect  
    读取layout并且同步重render。
*   useDebugValue  
    用于在自定义hook中，显示label在DevTools中。

## HOW
### How to use them?

### How are they be implemented?