# Sistema-de-Armazenamento
Foi um sistema básico desenvolvido por mim e meus colegas de turma, onde ele armazena o usuário que cadastrar seu nome e seu CPF no mesmo


package br.com.armazenamento_usuarios;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class ArmazenamentoUsuariosApplication {

	public static void main(String[] args) {
		SpringApplication.run(ArmazenamentoUsuariosApplication.class, args);
	}

}

------------------------------------------------------------------------------------------

package br.com.armazenamento_usuarios.controller;

import br.com.armazenamento_usuarios.model.Usuario;
import br.com.armazenamento_usuarios.repository.UsuarioRepository;
import java.util.Optional;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;

/**
 *
 * @author glaub
 */
@Controller
public class UsuarioController {
    @Autowired
    private UsuarioRepository usuarioRepository;
    
    @GetMapping("/gerenciarUsuarios")
    public String listarUsuarios(Model model) {
        model.addAttribute("listaUsuario", usuarioRepository.findAll());
        return "gerenciar_usuarios";
    }

    @GetMapping("/novoUsuario")
    public String novoUsuario(Model model) {
        model.addAttribute("usuario", new Usuario());
        return "editar_usuario";
    }

    @GetMapping("/editarUsuario/{Id}")
    public String editarUsuario(@PathVariable("Id") long Id, Model model) {
        Optional<Usuario> usuario = usuarioRepository.findById(Id);
        model.addAttribute("usuario", usuario.get());
        return "editar_usuario";
    }

    @PostMapping("/salvarUsuario")
    public String salvarUsuario(Usuario usuario, BindingResult result) {
        if (result.hasErrors()) {
            return "editar_usuario";
        }
        usuarioRepository.save(usuario);
        return "redirect:/gerenciarUsuarios";
    }

    @GetMapping("/excluirUsuarios/{Id}")
    public String excluirUsuarios(@PathVariable("Id") long Id) {
        usuarioRepository.deleteById(Id);
        return "redirect:/gerenciarUsuarios";
    }
}

--------------------------------------------------------------------------------------------------------

package br.com.armazenamento_usuarios.model;

import java.io.Serializable;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

/**
 *
 * @author glaub
 */
@Entity
public class Usuario implements Serializable{
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private long Id;
    private String nome;
    private String cpf;

    
    public long getId() {
        return Id;
    }

    
    public void setId(long Id) {
        this.Id = Id;
    }

    
    public String getNome() {
        return nome;
    }

    
    public void setNome(String nome) {
        this.nome = nome;
    }

    /**
     * @return the cpf
     */
    public String getCpf() {
        return cpf;
    }

    /**
     * @param cpf the cpf to set
     */
    public void setCpf(String cpf) {
        this.cpf = cpf;
    }
    
}

-------------------------------------------------------------------------------------

package br.com.armazenamento_usuarios.repository;

import br.com.armazenamento_usuarios.model.Usuario;
import java.util.Optional;
import org.springframework.data.repository.CrudRepository;

/**
 *
 * @author glaub
 */
public interface UsuarioRepository extends CrudRepository<Usuario,Long> {

}

--------------------------------------------------------------------------------------
<!DOCTYPE html>
<html>
    <head>
        <title>index</title>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
    </head>
    <body>
        <table>
            <tr>
            <ul>Nome: Glauber Luiz Castelo Branco Alves | Matrícula: 202002563958</ul>
            <ul>Nome: Jhonatan Alves Gonçalves | Matrícula: 202002410361</ul>
            <ul>Nome: Matheus Araujo Godoi do Nascimento | Matrícula: 202001521666</ul>
            </tr>
        </table>
    </body>
</html>

--------------------------------------------------------------------------------------

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org">
    <head> 
        <meta charset="utf-8"/>
        <title>Sistema de Cadastro de Usuários</title>
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
	<link rel="stylesheet" type="text/css" href="https://cdn.datatables.net/1.10.24/css/dataTables.bootstrap5.min.css">
	<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.4.1/font/bootstrap-icons.css">
        <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js" integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/js/bootstrap.min.js" integrity="sha384-ChfqqxuZUCnJSK3+MXmPNIyE6ZbWh2IMqE241rYiqJxyMiZ6OW/JmZQ5stwEULTy" crossorigin="anonymous"></script>
	<script type="text/javascript" language="javascript" src="https://cdn.datatables.net/1.10.24/js/jquery.dataTables.min.js"></script>
	<script type="text/javascript" language="javascript" src="https://cdn.datatables.net/1.10.24/js/dataTables.bootstrap5.min.js"></script>
    </head>
    <body>
        <div class="container" style="margin-top: 50px; margin-bottom: 20px">
            <h2 class="alert alert-dark">Gerenciar Usuários</h2> 
            <p class="text-right"><a href="/novoUsuario" class="btn btn-primary"> Novo Usuário </a></p>
        </div> 
        <div class="container">
            <table id="TabelaUsuarios" class="table table-striped">
                <thead>
                    <tr>
                        <th>ID</th>
                        <th>Nome</th>
                        <th>CPF</th>
                        <th>Alterar</th>
                        <th>Excluir</th>
                    </tr>
                </thead>
                <tbody>
                    <tr th:each="p : ${listaUsuario}">
                        <td th:text="${p.Id}"></td>
                        <td th:text="${p.nome}"></td>
                        <td th:text="${p.cpf}"></td>
                        <td><a th:href="@{/editarUsuario/{id}(id=${p.Id})}" class="btn btn-warning"><i class="bi bi-pencil"></i></a></td>
                        <td><a th:href="@{/excluirUsuarios/{id}(id=${p.Id})}" class="btn btn-danger"><i class="bi bi-trash"></i></a></td>
                    </tr>
                </tbody>
            </table>
        </div>
        <script>
            $(document).ready( function () {
                $('#TabelaUsuarios').DataTable({
                    "language": {
                        "sProcessing":    "Procesando...",
                        "sLengthMenu":    "Exibir _MENU_ registros por página",
                        "sZeroRecords":   "Nenhum resultado encontrado",
                        "sEmptyTable":    "Nenhum resultado encontrado",
                        "sInfo":          "Exibindo do _START_ até _END_ de um total de _TOTAL_ registros",
                        "sInfoEmpty":     "Exibindo do 0 até 0 de um total de 0 registros",
                        "sInfoFiltered":  "(Filtrado de um total de _MAX_ registros)",
                        "sInfoPostFix":   "",
                        "sSearch":        "Buscar:",
                        "sUrl":           "",
                        "sInfoThousands":  ",",
                        "sLoadingRecords": "Carregando...",
                        "oPaginate": {
                            "sFirst":    "Primeiro",
                            "sLast":    "Último",
                            "sNext":    "Próximo",
                            "sPrevious": "Anterior"
                        },
                        "oAria": {
                            "sSortAscending":  ": Ativar para ordenar a coluna de maneira ascendente",
                            "sSortDescending": ": Ativar para ordenar a coluna de maneira descendente"
                        }
                    }
                });
            } );
        </script>
    </body>
</html>

-----------------------------------------------------------------------------------------------

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org">
    <head> 
        <meta charset="utf-8"/>
        <title>Sistema Edição de Usuários</title>
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
        <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js" integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/js/bootstrap.min.js" integrity="sha384-ChfqqxuZUCnJSK3+MXmPNIyE6ZbWh2IMqE241rYiqJxyMiZ6OW/JmZQ5stwEULTy" crossorigin="anonymous"></script>
    </head>
    <body>
        <div class="container" style="margin-top: 50px">
            <h2 class="alert alert-dark">Editar Usuário</h2>
            <form action="#" th:action="@{/salvarUsuario}" th:object="${usuario}" method="post">
                <input type="hidden" th:field="*{Id}" id="Id">
                <hr class="mb-4">
                <div class="row">
                    <div class="col-md-12 mb-3">
                        <label for="nome">Nome: </label>
                        <input type="text" minlength="1" th:field="*{nome}" id="nome" placeholder="Nome do Usuario" class="form-control">
                        <label for="idCpf">CPF: </label>
                        <input type="text" maxlength="11" th:field="*{cpf}" id="cpf" placeholder="CPF do Usuario" class="form-control">
                    </div>    
                    <span th:if="${#fields.hasErrors('nome')}" th:errors="*{nome}" class="alert alert-warning"></span>                        
                </div>
                <hr class="mb-4">
                <input type="submit" value="Salvar" class="btn btn-success btn-block">
                <a href="/gerenciarUsuarios" class="btn btn-secondary btn-block" >Fechar</a>
            </form>
        </div>
    </body>
</html>
--------------------------------------------------------------------------------------------------------
