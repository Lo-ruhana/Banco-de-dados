# Banco-de-dados

Abordando assuntos por partes.

a) Elabore uma consulta que retorne os códigos de barras dos produtos cadastrados pela primeira vez no banco nos últimos 10 dias.

SELECT DISTINCT p.Código_de_barras
FROM Produto p
WHERE p.Data_cadastro >= DATE_SUB(CURDATE(), INTERVAL 10 DAY);

b) Elabore uma consulta que retorne os nomes das origens que tiveram algum produto cadastrado ou atualizado no mês de março de 2020.

SELECT DISTINCT o.Nome
FROM Origem o
INNER JOIN Produtoinfo pi ON o.Id = pi.IDOrigem
WHERE MONTH(pi.Data_Atualização) = 3 AND YEAR(pi.Data_Atualização) = 2020;

c) Houve um erro na atualização da origem “DISTRIBUIDORA TESTE” em que todos os produtos do fabricante “JOAO” foram enviados com nome “MARIA”. Faça um update que troque os produtos da “DISTRIBUIDORA TESTE” com fabricante “MARIA” para o fabricante “JOAO”.

UPDATE Produtoinfo
SET IDFabricante = (SELECT ID FROM Fabricante WHERE Nome = 'JOAO')
WHERE IDOrigem = (SELECT ID FROM Origem WHERE Nome = 'DISTRIBUIDORA TESTE')
AND IDFabricante = (SELECT ID FROM Fabricante WHERE Nome = 'MARIA');

d) Elabore uma consulta que retorne os códigos de barras, descrição, nome do fabricante e código interno de todos os produtos cadastrados de acordo com a ordem de preferência, onde um código de barras aparecerá apenas uma vez no resultado com as informações da origem de maior preferência disponível para ele.

SELECT pi.Código_Interno, p.Código_de_barras, pi.Descrição, f.Nome AS Nome_Fabricante
FROM Produtoinfo pi
INNER JOIN Produto p ON pi.IDProduto = p.ID
INNER JOIN Fabricante f ON pi.IDFabricante = f.ID
WHERE (pi.IDOrigem, pi.IDProduto, pi.Data_Atualização) IN (
    SELECT IDOrigem, IDProduto, MAX(Data_Atualização)
    FROM Produtoinfo
    GROUP BY IDProduto
)
ORDER BY pi.IDOrigem, pi.Preferencia DESC;

