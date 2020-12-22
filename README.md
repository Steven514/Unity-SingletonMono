# Unity-SingletonMono
Summary:  
Easily extent a monobehaviour class into a singleton that one owuld expect a singleton to do (make sure not to duplicate the class, etc) 

Description:  
Base class includes the unity function Awake() (not overridable -- for good reason) while having a custom OnAwake() called at the end of the Awake(), after making sure there is not duplicate singleton instance object in the scene.
 
Purpose:  
Extend the singleton setup and the ability to say if we want to destroy the single on scene load (inspector field). Override a custom OnAwake() function so we don't hit reflection twice from overriding Awake(), and check if a duplicate is already in the scene while the singleton instance is already active to destroy it.

How to Use:  
Create a class you want to make a singleton, and have it extend the generic type of the singleton. Set if you want your singleton to live across scenes or not.
```
public class YourClass : Singleton<YourClass>
{
    protected virtual void OnAwake() { 
      //Do what you want here if this class needs Awake();
    }
}


public abstract class Singleton<T> : MonoBehaviour where T : MonoBehaviour
{
    [Header("Base Settings")]
    [Tooltip("Set to true if you want your singleton to exist across scenes.")]
    [SerializeField] private bool _dontDestroyOnLoad = false;

    private static T _instance;

    public static T instance {
        get {
            if (_instance == null)
            {
                _instance = FindObjectOfType<T>();

                //Check if object was found in the scene.
                if (_instance == null)
                {
                    Debug.LogError($"Failed to find singleton of type {typeof(T)} in scene. Object does not exist.");
                }                     
            }
            return _instance;
        }
    }
    
    private void Awake()
    {
        if (instance != this && instance != null)
        {
            Destroy(gameObject);
            return;
        }

        if (_dontDestroyOnLoad == true)
        {
            DontDestroyOnLoad(gameObject);
        }

        OnAwake();
    }
        
    protected virtual void OnAwake() {}
}
```

