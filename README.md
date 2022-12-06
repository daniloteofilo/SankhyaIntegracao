# INTEGRAÇÃO BÁSICA DE SISTEMAS VIA SANKHYA, POR DANILO TEOFILO ~https://github.com/daniloteofilo

## Pequeno manual de instruções para que devs que utilizam o ERP SANKHYA consigam entender e fazer uma integração de sistemas via API com os métodos *POST* e *GET*.

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
-Conhecimentos da plataforma SANKHYA:
```
-Conhecimentos básicos sobre o sistema Sankhya.
-Compreender consultas na tela de DBEXPLORER(MySQL).
-Entender como funciona tela de construtor de telas. --universidade sankhya >https://ead.sankhya.com.br/html/videos.php?curso=2501&up=1
-Saber utilizar a tela de módulo java. >https://www.youtube.com/watch?v=m8LjHp5k3SI
```

### 🔧 Implementação

Essa etapa será com os seguintes passos:

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
================CÓDIGO=================
```
/*  ATENÇÃÃÃÃÃÃÃÃO, MUDE TODOS OS NOMES DE PACOTES, CLASSES E VARIAVEIS PARA QUE FAÇAM SENTIDO NO SEU PROJETO*/


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

```
Baixe todas as libs relacionadas ao SDK que o sankhya necessita para rodar e faça os imports no seu workspace.  ->https://developer.sankhya.com.br/docs/sdk-sankhya
```

```
Note que no seu código copiado da explicação, a maioria dos imports não estarão funcionando, tente clicar nos erros e fazer os imports recomendados, alguns deles serão diretamente do SDK do sankhya. Para alguns imports, vc terá que fazer a aquisição da lib de forma manual, baixe todas as libs necessárias e manualmente inclua nas dependências do seu projeto, faça as devidas correções que o código irá pedir.
```

```
Quando o código estiver "setado" de maneira que seu IDE não esteja reconhecendo erros, faça o EXPORT do seu projeto com a extensão .jar, para que seja possível fazer o upload dele no *Sankhya* para a tela de MÓDULO JAVA. A grande dica aqui é que você consiga fazer o código rodar de maneira que o resultado desejado seja alcançado, já que para debugar dentro do próprio sankhya seria bem mais complicado.
```


## 🛠️ Construído com
```

```

## 📌 Versão

tutorial ainda em construção.

## ✒️ Autor

Danilo Teófilo  
Me adicione no linkedin(https://www.linkedin.com/in/danilo-domingos-fonseca-teofilo-287477162/)


## 📄 Licença

Projeto livre!

## 🎁 Expressões de gratidão

* Se curtiu, deixe uma star no projeto e ajude esse dev iniciante com seu apoio moral 📢;


