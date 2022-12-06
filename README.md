## INTEGRAÇÃO BÁSICA DE SISTEMAS VIA SANKHYA COM OS MÉTODOS *POST* E *GET*, POR DANILO TEOFILO & TALES SILVA.

### Pequeno manual de instruções para que devs que utilizam o ERP SANKHYA consigam entender e fazer uma integração de sistemas via API com os métodos *POST* e *GET* via botão de ação.

## 🚀 Começando

Essas instruções permitirão que você consiga realizar uma comunicação entre o sankhya e a API que você tem como objetivo fazer a comunicação.

### 📋 Pré-requisitos

-Ter o sistema Sankhya instalado na máquina.

-Java na versão necessária.

-IDE de sua preferência.

-Importar no seu workspace as bibliotecas que contém as extensões necessárias para o SANKHYA compreender seu código. >https://comunidade.sankhya.com.br/t/dica-como-baixar-bibliotecas-nativas-jars/4159

-Conhecimentos de programação:
```
-Lógica de programação, preferencialmente em JAVA.	>https://loiane.training/continuar-curso/java-basico
-Entender o que é uma API, qual a sua finalidade, como funciona e sua estrutura de dados.	>https://aws.amazon.com/pt/what-is/api/
-Saber como consumir uma API e entender os diferentes tipos de "METHOD".	>https://arthur-almeida.medium.com/consumindo-uma-api-de-maneira-simples-com-java-2a386010e4b9
-SQL.	--Curso de sql > https://www.youtube.com/watch?v=G7bMwefn8RQ
```

-Ter uma tela construída e lançada no menu Construtor de Telas.

-Conhecimentos da plataforma SANKHYA:
```
-Conhecimentos básicos sobre o sistema Sankhya.
-Compreender consultas na tela de DBEXPLORER(MySQL).
-Entender como funciona tela de construtor de telas. --universidade sankhya >https://ead.sankhya.com.br/html/videos.php?curso=2501&up=1
-Saber utilizar a tela de módulo java. >https://www.youtube.com/watch?v=m8LjHp5k3SI
```



### 🔧 Implementação

#### Essa etapa será com os seguintes passos:

```
Primeiramente iremos preparar o arquivo de código java que executará toda a ação.
```

```
Abra a IDE da sua preferência e mãos na massa, eu utilizo eclipse.
```

```
Criar um novo Java Project(projeto java) com o nome que fizer sentido acompanhado do prefixo "br."(Exemplo:  br.meuProjeto), visualize o quadro JRE, no primeiro select você vai colocar a versão JavaSE1.8
```

```
Criar um novo package(pacote) dentro do projeto java criado anteriormente.
```

```
Adicionar uma nova class dentro do pacote criado anteriormente.
```

```
Abrir o código da class criada e colar nele o código de integração a seguir com as alterações necessárias para o seu projeto.
A primeira vista, vai ter muito erro no código, mas iremos resolver tudo com as devidas importações.

```
================CÓDIGO METHOD POST =================
```
/*  ATENÇÃÃÃÃÃÃÃÃO, MUDE TODOS OS NOMES DE PACOTES, CLASSES E VARIAVEIS PARA QUE FAÇAM SENTIDO NO SEU PROJETO*/


/* O CÓDIGO ABAIXO ESTA CONSULTANDO UMA TABELA E ARMAZENANDO OS VALORES DA CONSULTA EM VARIÁVEIS E ENVIANDO PARA A API DEFINIDA, LEMBRANDO QUE AS CONSULTAS PODEM
SER ALIMENTADAS COM PARÂMETROS QUE SÃO PREENCHIDOS NO PRÓPRIO SANKHYA */


package br.nomeDoProjeto;

import java.sql.ResultSet;

import com.sankhya.util.JdbcUtils;

import br.com.sankhya.extensions.actionbutton.AcaoRotinaJava;
import br.com.sankhya.extensions.actionbutton.ContextoAcao;
import br.com.sankhya.jape.EntityFacade;
import br.com.sankhya.jape.core.JapeSession;
import br.com.sankhya.jape.core.JapeSession.SessionHandle;
import br.com.sankhya.jape.dao.JdbcWrapper;
import br.com.sankhya.jape.sql.NativeSql;
import br.com.sankhya.modelcore.MGEModelException;
import br.com.sankhya.modelcore.util.EntityFacadeFactory;
import okhttp3.FormBody;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.RequestBody;
import okhttp3.Response;


public class nomeDaClass implements AcaoRotinaJava {

	@Override
	public void doAction(ContextoAcao arg0) throws Exception {
		// TODO Auto-generated method stub
		JdbcWrapper jdbc = null;
		NativeSql sql = null;
		ResultSet rset = null;
		SessionHandle hnd = null;
		
		try {
			hnd = JapeSession.open();
			hnd.setFindersMaxRows(-1);
			EntityFacade entity = EntityFacadeFactory.getDWFFacade();
			jdbc = entity.getJdbcWrapper();
			jdbc.openSession();

			sql = new NativeSql(jdbc);

			sql.appendSql(/*SUA CONSULTA*/);

			/* EXEMPLO DE CONSULTA    >>>> sql.appendSql("SELECT TESTETXT AS TESTE, CODTESTEDANILO AS CODTESTE\r\n"
								      + "FROM AD_TESTEDANILO"); */

			

			rset = sql.executeQuery();
			
			/*O WHILE ABAIXO FICARA RESPONSÁVEL POR ARMAZENAR OS DADOS DA CONSULTA NAS VARIAVEIS COM NOMES QUE FIZEREM SENTIDO PARA O CÓDIGO
			, NESSE CASO A VARIAVEL1 E VARIAVEL2 ESTÃO ARMAZENANDO OS VALORES DO EXEMPLO DE CONSULTA DAS COLUNAS "TESTE" E "CODTESTE" RESPECTIVAMENTE */
				
			while (rset.next()) {
				String VARIAVEL1 = rset.getString("TESTE");
				String VARIAVEL2 = rset.getString("CODTESTE");
				
				armazenaValores(VARIAVEL1, VARIAVEL2);
				
				
			}

		} catch (Exception e) {
			MGEModelException.throwMe(e);
		} finally {
			JdbcUtils.closeResultSet(rset);
			NativeSql.releaseResources(sql);
			JdbcWrapper.closeSession(jdbc);
			JapeSession.close(hnd);

		}
		
		
	}

	private void armazenaValores(String VARIAVEL1, String VARIAVEL2) {
		// TODO Auto-generated method stub
		

		try {
			OkHttpClient client = new OkHttpClient().newBuilder().build();
			
			@SuppressWarnings("unused")
			okhttp3.MediaType mediaType = okhttp3.MediaType.parse("text/plain");
			
			RequestBody body = new FormBody.Builder()
				      .add("/*nome da chave do JSON que você deseja enviar a informação dessa variável*/", VARIAVEL1)
				      .add("/*nome da chave do JSON que você deseja enviar a informação dessa variável*/", VARIAVEL2)
				      .build();
			
			String urlApi = "/*url completa da sua api, se for necessário algum tipo de token, o token deve ser passado junto a url da API*/";
			Request request = new Request.Builder()
					  .url(urlApi)
					  .method("POST", body)
					  .addHeader("Content-Type", "application/json")
					  .build();
						
			Response response = client.newCall(request).execute();

			String result = response.body().string(); // capturando resposta da requisicao

		} catch (Exception e) {

		} finally {

		}
		
	}

}


```

===============FIM DO CÓDIGO=============

```
O código acima está preparado para enviar informações para a sua API utilizando o METHOD *POST* com OkHttp, se a sua necessidade for outro METHOD, faça as devidas adaptações ao código.  -> https://www.baeldung.com/guide-to-okhttp
```

================CÓDIGO METHOD GET =================

```
/*  ATENÇÃÃÃÃÃÃÃÃO, MUDE TODOS OS NOMES DE PACOTES, CLASSES E VARIAVEIS PARA QUE FAÇAM SENTIDO NO SEU PROJETO*/

/* O CÓDIGO ABAIXO ESTA RECEBENDO AS INFORMAÇÕES DE UMA API E FAZENDO O INCREMENTO DA TABELA DO SANKHYA COM O VALOR ARMAZENADO NA VARIÁVEL, LEMBRANDO QUE OS VALORES PODEM SER DEFINIDOS COM PARÂMETROS QUE SÃO PREENCHIDOS NO PRÓPRIO SANKHYA */


package br.nomeDoProjeto;

import java.math.BigDecimal;


import org.json.JSONArray;
import org.json.JSONObject;

import br.com.sankhya.extensions.actionbutton.AcaoRotinaJava;
import br.com.sankhya.extensions.actionbutton.ContextoAcao;
import br.com.sankhya.jape.core.JapeSession;
import br.com.sankhya.jape.vo.DynamicVO;
import br.com.sankhya.jape.wrapper.JapeFactory;
import br.com.sankhya.jape.wrapper.JapeWrapper;
import br.com.sankhya.modelcore.MGEModelException;
import okhttp3.OkHttpClient;
import okhttp3.Request;

public class testeGet implements AcaoRotinaJava{
	public void doAction(ContextoAcao contexto) throws Exception  {
		OkHttpClient client = new OkHttpClient().newBuilder()
				  .build();
				okhttp3.MediaType mediaType = okhttp3.MediaType.parse("text/plain");
				String urlApi = "/*url completa da sua api, se for necessário algum tipo de token, o token deve ser passado junto a url da API*/";
				
				Request request = new Request.Builder()
				  .url("urlApi")
				  .method("GET", null)
				  .build();
				okhttp3.Response response = client.newCall(request).execute();
				String result = response.body().string();
				
				JSONArray jsonObject = new JSONArray(result);
				
				/*NA VARIÁVEL ABAIXO "teste",ESTOU ARMAZENANDO APENAS O PRIMEIRO INDICE DA API, RECOMENDO FORTEMENTE QUE VOCÊ USE ALGUM TIPO DE LAÇO DE 
				REPETIÇÃO PARA PEGAR TODOS OS DADOS DO SEU INTERESSE, ESTUDE SOBRE COMO TRATAR DADOS JSON EM JAVA */
				
				JSONObject teste = jsonObject.getJSONObject(0);
				
				String cidade = teste.getString("id");
				BigDecimal cidade2 = new BigDecimal(cidade);
				
				String name = teste.getString("name");
				
				inserirSankhya(cidade2, name);
				
	}

	private static void inserirSankhya(BigDecimal cidade2, String name) throws MGEModelException {
		// TODO Auto-generated method stub
		
		JapeSession.SessionHandle hnd = null;
		try {
			hnd = JapeSession.open();
			JapeWrapper empresaDAO = JapeFactory.dao("AD_TESTEDANILO");
			DynamicVO save = empresaDAO.create()
				.set("CODTESTEDANILO", cidade2)
				.set("TESTETXT", name)
				.save();
			
		} catch (Exception e) {
			MGEModelException.throwMe(e);
		} finally {
			JapeSession.close(hnd);
		}
	}
}





```

===============FIM DO CÓDIGO=============

```
O código acima está preparado para receber informações de uma API utilizando o METHOD *GET* com OkHttp, se a sua necessidade for outro METHOD, faça as devidas adaptações ao código.  -> https://www.baeldung.com/guide-to-okhttp
```


```
Baixe todas as libs relacionadas ao SDK que o sankhya necessita para rodar e faça os imports no seu workspace.  ->https://developer.sankhya.com.br/docs/sdk-sankhya
```

```
Note que no seu código copiado da explicação, a maioria dos imports não estarão funcionando, tente clicar nos erros e fazer os imports recomendados, alguns deles serão diretamente do SDK do sankhya. Para alguns imports, você terá que fazer a aquisição da lib de forma manual, baixe todas as libs necessárias e manualmente inclua nas dependências do seu projeto, depois faça as devidas correções que o código irá pedir.
```

```
Quando o código estiver "setado" de maneira que seu IDE não esteja reconhecendo erros, faça o EXPORT do seu projeto com a extensão .jar, para que seja possível fazer o upload dele no *Sankhya* para a tela de MÓDULO JAVA. A grande dica aqui é que você consiga fazer o código rodar de maneira que o resultado desejado seja alcançado, já que para debugar dentro do próprio sankhya seria bem mais complicado.
```

#### Para fazer o teste, basta seguir os próximos passos.

```
Ir ao menu "Construtor de Telas", clicar na aba "Ações" e adicionar uma nova ação.
```
*OS CAMPOS OBRIGATÓRIOS PODEM SER PREENCHIDOS COM A SEGUINTE RECOMENDAÇÃO:*
```
 No campo de descrição 
	coloque um nome que faça sentido, de preferência que seja um verbo que reflita o que o seu código está programado para fazer,
		 defina o tipo como "Rotina Java", 
			escolha o módulo que você criou na tela "Módulo Java",
				 clique em *Baixar biblioteca de extensões* e escolha a classe que contém o código que será executado.
```

Depois seguiremos para nossa tela criada, assim como foi definido nos pré-requisitos desse manual.
	Clique no botão de ação
		Execute a ação nomeada no campo de descrição
			SE TIVER DADO TUDO CERTO, O CÓDIGO SIMPLESMENTE EXECUTARÁ E NÃO APARECERÁ NENHUMA MENSAGEM, 
				VERIFIQUE APENAS SE A API SETADA NO CÓDIGO FOI DE FATO INCREMENTADA COM AS NOVAS INFORMAÇÕES ENVIADAS.


## 🛠️ Construído com
```
Esse tutorial foi construído principalmente por mim(Danilo) com pouco conhecimento em Java e mostra de maneira básica como podem ser feitas algumas requisições pela plataforma SANKHYA, com exemplos dos métodos *GET* e *POST*.
Para sugestões de melhora no código, possíveis correções e dúvidas, consulte os autores.
```

## 📌 Versão

tutorial ainda em construção.

## ✒️ Autores

Danilo Teófilo  
Me adicione no linkedin ->https://www.linkedin.com/in/danilo-domingos-fonseca-teofilo-287477162/

Tales Silva </br>
Me adicione no linkedin -> https://www.linkedin.com/in/tales-silva-050731256/


## 📄 Licença

Projeto livre!

## 🎁 Expressões de gratidão

* Se curtiu, deixe uma star no projeto e ajude esse dev iniciante com seu apoio moral 📢;


