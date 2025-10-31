using System.Collections;
using UnityEditor.Build.Content;
using UnityEngine;
using UnityEngine.Rendering;

public class PlayerController : MonoBehaviour
{
    public GameObject saveHUD;

    public class DebugCollisions : MonoBehaviour
    {
        void OnTriggerEnter(Collider other)
        {
            Debug.Log($"🔵 TRIGGER ENTER: {other.name} | Tag: {other.tag}");
        }

        void OnCollisionEnter(Collision collision)
        {
            Debug.Log($"🔴 COLLISION ENTER: {collision.gameObject.name} | Tag: {collision.gameObject.tag}");
        }
    }
    [Header("Player Stats")]

    public int vida = 100;
    public int QuantidadeItens = 0;

    [Header("Movement")]

    public float moveSpeed = 5f;

    

    void Update ()
    {
        HandleMovement();

        if(GameManager.Instance != null)
        {
            GameManager.Instance.UpdatePlayerData(vida, QuantidadeItens,transform.position);

        }
    }
    void HandleMovement()
    {
        float horizoltal = Input.GetAxis("Horizontal");
        float vertical = Input.GetAxis("Vertical");

        Vector3 movement = new Vector3(horizoltal, 0f , vertical) * moveSpeed * Time.deltaTime;
        
        transform.Translate(movement);
    }
    private void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("Item"))
        {
            ColetarItem(other.gameObject);
            saveHUD.SetActive(true);
        }
        else if (other.CompareTag("Dano"))
        {
            LevarDano(10);
            saveHUD.SetActive(true);

        }
        StartCoroutine(SalvamentoAuto());
    }
    void ColetarItem(GameObject item)
    {
        QuantidadeItens++;
        Destroy(item);

        GameManager.Instance.ShowSaveMessage();
        GameManager.Instance.SavePlayerData();

        Debug.Log("Item coletado! Total: " + QuantidadeItens);


    }

    private IEnumerator SalvamentoAuto()
    {
        yield return new WaitForSeconds(3);
        saveHUD.SetActive(false);
    }
    void LevarDano(int dano)
    {
        vida -= dano;
        vida = Mathf.Max(0, vida);

        GameManager.Instance.SavePlayerData();
        Debug.Log("Vida:" + vida);

        if (vida < 0) Morrer();
    }
    void Morrer()
    {
        Debug.Log("Player morreu!");

    }
    public void SetPlayerData(int vida, int itens, Vector3 position)
    {
        this.vida = vida;
        this.QuantidadeItens = itens;
    }

}
