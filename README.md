# SIG3 — Pacotes de atualização

Este repositório guarda **as versões publicadas do SIG3 Web** — os arquivos que os servidores dos
clientes baixam para se atualizar sozinhos.

Aqui **não há código-fonte.** Só o manifesto que diz qual é a versão atual e os pacotes prontos,
anexados a cada *release*.

## O que tem aqui

| Arquivo | O que é |
|---|---|
| `versao.json` | O manifesto. Diz qual é a versão publicada, onde baixá-la, e como conferir que ela é legítima. É o único arquivo que os clientes consultam de hora em hora. |
| `notas-versao.json` | O que mudou em cada versão, em português. É o mesmo texto que aparece na tela **Sistema › Versão do sistema** de quem já atualizou. |
| *Releases* | Cada versão publicada tem um *release* com o `.zip` do sistema anexado. É de lá que o download sai. |

O `.zip` não fica versionado no git de propósito: ele passa de 100 MB, e o git guardaria **todas**
as versões para sempre — em pouco tempo, clonar este repositório custaria alguns gigabytes. Como
*release*, cada pacote fica disponível pela URL sem pesar no histórico.

## Por que é público

Porque o que protege a atualização **não é o sigilo, é a assinatura.**

Cada pacote é assinado com uma chave privada que existe num único computador, e que nunca esteve
neste repositório nem em nenhum outro. O servidor de cada cliente carrega a chave **pública**
correspondente compilada dentro do próprio programa, e só aceita um pacote cuja assinatura bata.

Consequência prática: se alguém invadir este repositório e trocar o `.zip` por outro, **nenhum
cliente instala.** O pacote é recusado antes de qualquer coisa ser alterada, e o servidor registra
`PACOTE DE ATUALIZAÇÃO REJEITADO` no log para que a gente saiba que aconteceu.

Um repositório privado não deixaria isso mais seguro — só exigiria guardar uma senha dentro de cada
servidor de cliente, que é justamente onde uma senha não deveria estar.

## Como um cliente se atualiza

1. O servidor do cliente lê o `versao.json` deste repositório, uma vez por dia.
2. Se a versão publicada for mais nova que a dele, baixa o `.zip` do *release*.
3. **Confere, nesta ordem:** o tamanho, depois o `sha256` do conteúdo, depois a assinatura.
   Qualquer uma que não bata, o pacote é descartado e nada é alterado.
4. Deixa a versão nova pronta ao lado da atual, sem trocar nada ainda.
5. A troca acontece de madrugada, ou na próxima vez que o computador ligar — o que vier primeiro.
   A versão anterior fica guardada; **se a nova não subir, o sistema volta sozinho para ela.**

Nada disso exige alguém no cliente clicando em nada.

## Conferir um pacote por fora

Se quiser verificar um download à mão, o `sha256` do manifesto é o do arquivo inteiro:

```powershell
Get-FileHash .\sig3-1.1.0.zip -Algorithm SHA256
```

O valor tem de bater com o campo `sha256` do `versao.json`. (Isso confere o **conteúdo**; quem
confere a **origem** é a assinatura, e para isso é preciso a chave pública, que está dentro do
programa.)

---

O código do SIG3 Web é privado e vive em outro lugar. Dúvidas sobre o sistema em si não são deste
repositório.
