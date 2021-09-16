# ScriptableObject

* 스크립팅이 가능한 오브젝트를 만드는 클래스

* `ScriptableObject`를 상속시키거나 `MonoBehavior`를 상속받지 않게 함으로서 사용이 가능

  

### Example

```C#
[CreateAssetMenu(fileName="New HeroData", menuName="Sword HeroData", order=2)]
public class test : ScriptableObject 
{
    [SerializeField] string heroName;
    [SerializeField] string description;
    [SerializeField] int goldCost;
    
    public string sName => heroName;
    public string sDesc => description;
    public int nCoin => goldCost;
}
```

```c#
public class Scene : MonoBehavior
{
    public Text m_txtUI;
    public test m_data;
    
    void Start() {
        m_txtUI.text = string.Format(m_data.sName + "//" + m_data.sDesc);
    }
}
```

* 이처럼 외부에 데이터를 저장하거나 가져와서 사용할 수 있음

