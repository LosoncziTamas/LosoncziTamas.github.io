---
layout:     post
title:      Implementing a scrollable grid list in Unity
date:       2020-03-06
author:     Tamás Losonczi
categories: Unity3D
tags:
 - unity
 - ui
---

## First attempt

In order to implement a bare scrollable grid all you need is a `Scroll View` UI element where the content has a `Content Size Fitter` and a `Grid Layout Group` component. 
Since I wanted to replicate a level selector that only scrolls vertically, I set the _Vertical Fit_ parameter to _Min Size_ and disabled the horizontal property of the `Scroll Rect`. This makes the content expand according to the number of elements while it is only scrollable in one direction.

There are two custom scripts: `LevelSelectorItem` and `LevelSelector`. `LevelSelectorItem` holds references to UI elements and fills them with the proper values. To be able to instantiate items on the fly, we'll also need to create a prefab for the LevelSelectorItem. The prefab consists of a Text, a Button, an Image component and our `LevelSelectorItem` script.

```
    public class LevelSelectorItem : MonoBehaviour
    {
        [SerializeField] private Text _text;
        [SerializeField] private Button _button;
        
        private LevelSelector.ItemData _data;
                
        public void Init(LevelSelector.ItemData data)
        {
            _data = data;
            _text.text = data.levelText;
        }

        private void OnEnable()
        {
            _button.onClick.AddListener(HandleClick);
        }

        private void OnDisable()
        {
            _button.onClick.RemoveListener(HandleClick);
        }

        private void HandleClick()
        {
            _data?.onClick.Invoke(_data);
        }
    }
```

`LevelSelector` maintains a list of `ItemData` and instantiates/destroys objects. Here `_content` is a reference to the content game object which comes with the Scroll View.

```
    public class LevelSelector : MonoBehaviour
    {
        [SerializeField] private LevelSelectorItem _selectorItemPrefab;
        [SerializeField] private RectTransform _content;

        private List<ItemData> _items = new List<ItemData>();

        public class ItemData
        {
            public readonly Action<ItemData> onClick;
            public readonly string levelText;

            public ItemData(Action<ItemData> onClick, string levelText)
            {
                this.onClick = onClick;
                this.levelText = levelText;
            }
        }

        private void Clear()
        {
            _items.Clear();
            foreach (Transform child in _content.transform)
            {
                Destroy(child.gameObject);
            }
        }

        public void SetItems(List<ItemData> itemsToAdd)
        {
            _items.AddRange(itemsToAdd);
            foreach (var item in _items)
            {
                Instantiate(_selectorItemPrefab, _content.transform).Init(item);
            }
        }

        private void OnDestroy()
        {
            Clear();
        }
    }
```

In some cases, this minimal setup is perfectly fine, for example when you are making a prototype or you know that the number of items will be relatively small and you won't make changes frequently in the data set. Still, it has several pain points.

## Potential pitfalls & issues
- The removal of items is error-prone and not performant.
  - Consider a situation where you may want to clear the list first, and then add new items to the grid. Since `Destroy()` is not called instantaneously but after the current update loop has finished, the newly added items will be destroyed as well.
- A large number of items are not handled efficiently.
  - Items are destroyed and created each time the data set changes. 
- Harder to identify which item gets selected (one string field may not be sufficient to differentiate an item from the rest).
  - You can remedy this by adding a field to the `ItemData` which uniquely identifies it in the collection like an ID or a reference.
- Wrong item can get selected in the grid.
  - This can happen when the child is taking more size than specified cell size in the `Grid Layout Group`. 

## Second attempt
To tackle the unnecessary creation and destruction of objects we use a pool which buffers and returns object on demand.

```
    public class LevelSelectorPool : MonoBehaviour
    {
        [SerializeField] private LevelSelectorItem _itemPrefab;
            
        private readonly Stack<LevelSelectorItem> _pooledObjects = new Stack<LevelSelectorItem>();

        public LevelSelectorItem GetItem()
        {
            LevelSelectorItem result;
            
            if (_pooledObjects.Count > 0)
            {
                result =  _pooledObjects.Pop();
            }
            else
            {
                result = Instantiate(_itemPrefab);
            }
            result.transform.SetParent(null);
            result.gameObject.SetActive(true);

            return result;
        }

        public void ReturnItem(LevelSelectorItem toReturn)
        {
            toReturn.gameObject.SetActive(false);
            toReturn.transform.SetParent(transform);
            _pooledObjects.Push(toReturn);
        }

        public void Clear()
        {
            foreach(var item in _pooledObjects)
            {
                Destroy(item);   
            }
            _pooledObjects.Clear();
        }
    }
```

Additionally, we update the `LevelSelector` to use the pool. The pool has to be exposed to the `LevelSelector` in the hierarchy. We'll also make sure to set the scale explicitly because parenting and unparenting can mess up the size of the child.

```
    public class LevelSelector : MonoBehaviour
    {
        [SerializeField] private LevelSelectorItem _selectorItemPrefab;
        [SerializeField] private RectTransform _content;
        [SerializeField] private LevelSelectorPool _pool;

        private List<ItemData> _items = new List<ItemData>();

        public class ItemData
        {
            public readonly Action<ItemData> onClick;
            public readonly string levelText;
            public readonly object dataRef;

            public ItemData(Action<ItemData> onClick, string levelText, object dataRef = null)
            {
                this.onClick = onClick;
                this.levelText = levelText;
                this.dataRef = dataRef;
            }
        }

        private void Clear()
        {
            _items.Clear();
            while (_content.childCount > 0)
            {
                var child = _content.GetChild(0);
                _pool.ReturnItem(child.GetComponent<LevelSelectorItem>());
            }
        }

        public void SetItems(List<ItemData> itemsToAdd)
        {
            Clear();
            _items.AddRange(itemsToAdd);
            foreach (var itemData in _items)
            {
                var itemInstance = _pool.GetItem();
                itemInstance.Init(itemData);

                var itemTransform = itemInstance.transform;
                itemTransform.SetParent(_content.transform);
                itemTransform.localScale = Vector3.one;
            }
        }

        private void OnDestroy()
        {
            Clear();
        }
    }
```

Although this version is still not perfect or super performant, it works well under normal circumstances and can be easily customized to your needs.