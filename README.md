
# Spring TDD com JUnit  Repository

## Construa um spring em java 8

## Executando testes unitários em repository

- Antes de fazer o teste unitário vamos construir algumas camadas da aplicação

## Colocando as dependencias no pom.xml
```
<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>

		<dependency>
			<groupId>com.h2database</groupId>
			<artifactId>h2</artifactId>
			<scope>runtime</scope>
		</dependency>
			<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
		
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
			<exclusions>
				<exclusion>
					<groupId>org.junit.jupiter</groupId>
					<artifactId>junit-jupiter</artifactId>
				</exclusion>
				<exclusion>
					<groupId>org.junit.vintage</groupId>
					<artifactId>junit-vintage-engine</artifactId>
				</exclusion>
				<exclusion>
					<groupId>org.mockito</groupId>
					<artifactId>mockito-junit-jupiter</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-validation</artifactId>
		</dependency>
		<dependency>
			<groupId>org.hsqldb</groupId>
			<artifactId>hsqldb</artifactId>
		</dependency>
	</dependencies>
```

## Atualizando as bibliotecas do ApplicationTest

Atualize as bibliotecas do arquivo ApplicationTest

![alt text](https://i.imgur.com/r5zvS0E.png)

```
package com.generation.Junit2;

import org.junit.Test;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class Junit2ApplicationTests {

	@Test
	void contextLoads() {
	}

}
````````

### Model/Entity 

 1. Crie um pacote e o nomeie como Model ou Entity, em seguida cole o código abaixo;
 
```
    package integracao.bancodedados.model;
    
    import javax.persistence.Entity;
    import javax.persistence.GeneratedValue;
    import javax.persistence.GenerationType;
    import javax.persistence.Id;
    import javax.validation.constraints.NotEmpty;
    
    @Entity
    public class ContatoModel {
    
    	//ATRIBUTOS
    	@Id
    	@GeneratedValue(strategy=GenerationType.IDENTITY)
    	private Long id;
    	
    	@NotEmpty(message="O DDD deve ser preenchido")
    	private String ddd;
    	
    	@NotEmpty(message="O Telefone deve ser preenchido")
    	private String telefone;
    	
    	@NotEmpty(message="O Nome deve ser preenchido")
    	private String nome;
    
    	//CONTRUCTORS
    	public ContatoModel(){
    	}
    	
    	public ContatoModel(String nome, String ddd, String telefone) {
    		this.nome = nome;
    		this.ddd = ddd;
    		this.telefone = telefone;
    	}
    	
    	//GETTERS AND SETTERS
    	public Long getId() {
    		return id;
    	}
    
    	public void setId(Long id) {
    		this.id = id;
    	}
    
    	public String getDdd() {
    		return ddd;
    	}
    
    	public void setDdd(String ddd) {
    		this.ddd = ddd;
    	}
    
    	public String getTelefone() {
    		return telefone;
    	}
    
    	public void setTelefone(String telefone) {
    		this.telefone = telefone;
    	}
    
    	public String getNome() {
    		return nome;
    	}
    
    	public void setNome(String nome) {
    		this.nome = nome;
    	}
    } 



````````


### Crie uma Interface de repository para usar os recursos do JPAHibernat

2. Crie um pacote e o nomeio como repository e insira o código abaixo;

```
package integracao.bancodedados.repository;

import java.util.List;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import integracao.bancodedados.model.ContatoModel;

@Repository
public interface ContatoRepository extends JpaRepository<ContatoModel, Long> {

	public ContatoModel findFirstByNome(String nome);
	public List<ContatoModel> findAllByNomeIgnoreCaseContaining(String nome);

}

`````

Pronto! agora já temos um projeto onde possamos implementar uma classe de teste.


### Implementando os testes unitários.

> Nesta seção iremos realizar testes unitários nas nossas anotações de
> validações inseridas na nossa repository.

3. Crie um pacote em src/test/resource e crie um arquivo do tipo file e o nomeio como  application.properties;

	**apasta ficará assim:**
	![enter image description here](https://i.imgur.com/103R4kj.png)


	em seguida acesso o arquivo **application.properties** e insira o código abaixo para configurar a conexão com o banco de dados.

      `spring.jpa.hibernate.ddl-auto=update spring.datasource.url=jdbc:mysql://localhost/test_tdd_repository?createDatabaseIfNotExist=true&serverTimezone=UTC&useSSl=false spring.datasource.username=root spring.datasource.password=Admin357/ spring.jpa.show-sql=true`

4. Em src/test/java Crie um pacote e o nomeie como **contatos**, em seguida insira o código abaixo;

````
package integracao.bancodedados.contatos;

import java.util.List;

import org.junit.After;
import org.junit.Assert;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import integracao.bancodedados.model.ContatoModel;
import integracao.bancodedados.repository.ContatoRepository;

@RunWith(SpringRunner.class)
@SpringBootTest
public class ContatosRepositoryIntegrationTest {

	@Autowired
	private ContatoRepository contatoRepository;

	@Before
	public void start() {
		ContatoModel contato = new ContatoModel("Chefe", "0y", "9xxxxxxx9");
		if (contatoRepository.findFirstByNome(contato.getNome()) == null)
			contatoRepository.save(contato);

		contato = new ContatoModel("Novo Chefe", "0y", "8xxxxxxx8");
		if (contatoRepository.findFirstByNome(contato.getNome()) == null)
			contatoRepository.save(contato);

		contato = new ContatoModel("chefe Mais Antigo", "0y", "7xxxxxxx7");
		if (contatoRepository.findFirstByNome(contato.getNome()) == null)
			contatoRepository.save(contato);

		contato = new ContatoModel("Amigo", "0z", "5xxxxxxx5");
		if (contatoRepository.findFirstByNome(contato.getNome()) == null)
			contatoRepository.save(contato);
	}

	@Test
	public void findByNomeRetornaContato() throws Exception {

		ContatoModel contato = contatoRepository.findFirstByNome("Chefe");

		Assert.assertTrue(contato.getNome().equals("Chefe"));
	}

	@Test
	public void findAllByNomeIgnoreCaseRetornaTresContato() {

		List<ContatoModel> contatos = contatoRepository.findAllByNomeIgnoreCaseContaining("chefe");

		Assert.assertEquals(3, contatos.size());
	}

	@After
	public void end() {
		contatoRepository.deleteAll();
	}

}
````

![enter image description here](https://i.imgur.com/xBsk2tE.png)
![enter image description here](https://i.imgur.com/7PLlyyU.png)


[Link do github](ht
