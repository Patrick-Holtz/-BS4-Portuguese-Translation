Documentação Beautiful Soup
============================

.. image:: 6.1.jpg
   :align: right
   :alt: "The Fish-Footman began by producing from under his arm a great letter, nearly as large as himself."

`Beautiful Soup <http://www.crummy.com/software/BeautifulSoup/>`_ é uma biblioteca
Python para extrair dados de arquivos HTML e XML. Ela funcionará como seu analisador favorito
fornecendo maneiras idiomáticas de navegação, busca, e modificação da árvore de análise
sintática. Geralmente, salvando programadores em horas ou dias de trabalho.

Essas instruções ilustram todos os principais recursos do Beautiful Soup 4,
com exemplos. Mostrando para o quê a biblioteca é boa, como ela funciona, como utilizá-la, como
prepará-la para fazer o que você quer, e o que fazer quando ela não corresponde suas expectativas.


Os exemplos nessa documentação deverão funcionar da mesma maneira no Python
2.7 e Python 3.2.

Você talvez esteja procurando a documentação do `Beautiful Soup 3
<http://www.crummy.com/software/BeautifulSoup/bs3/documentation.html>`_.
Se sim, você deveria ter em mente que o Beautiful Soup 3 não está mais sendo
desenvolvido, e que o Beautiful Soup 4 é recomendado para todos os novos
projetos. Se você quiser entender as diferenças entre o Beautiful
Soup 3 e o Beautiful Soup 4, veja em `Importando código para BS4`_.

Obtendo ajuda
------------

Se você tiver dúvidas sobre o Beautiful Soup, ou tiver problemas,
`crie um tópico no grupo de discussão
<https://groups.google.com/forum/?fromgroups#!forum/beautifulsoup>`_. Se
seu problema envolve analisar um documento HTML, deixe claro mencionando
:ref:`O que a função diagnose() diz <diagnose>` sobre
esse documento.

Início Rápido
===========

Aqui está um documento HTML que vou usar como exemplo em todo este
documento. Faz parte de uma história de `Alice no País das Maravilhas`::

 html_doc = """
 <html><head><title>A história do Arganaz</title></head>
 <body>
 <p class="title"><b>A história do Arganaz</b></p>

 <p class="story">Era uma vez três irmãzinhas; e seus nomes eram
 <a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
 <a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> e
 <a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
 e elas viviam no fundo de um poço.</p>

 <p class="story">...</p>
 """

Executar o documento "three sisters" através da Beautiful Soup nos retorna
um objeto ``BeautifulSoup``, que representa o documento como uma estrutura
de dados aninhada::

 from bs4 import BeautifulSoup
 soup = BeautifulSoup(html_doc, 'html.parser')

 print(soup.prettify())
 # <html>
 #  <head>
 #   <title>
 #    A história do Arganaz
 #   </title>
 #  </head>
 #  <body>
 #   <p class="title">
 #    <b>
 #     A história do Arganaz
 #    </b>
 #   </p>
 #   <p class="story">
 #    Era uma vez três irmãzinhas; e seus nomes eram
 #    <a class="sister" href="http://example.com/elsie" id="link1">
 #     Elsie
 #    </a>
 #    ,
 #    <a class="sister" href="http://example.com/lacie" id="link2">
 #     Lacie
 #    </a>
 #    e
 #    <a class="sister" href="http://example.com/tillie" id="link2">
 #     Tillie
 #    </a>
 #    ; e elas viviam no fundo de um poço.
 #   </p>
 #   <p class="story">
 #    ...
 #   </p>
 #  </body>
 # </html>

Aqui estão algumas maneiras simples de navegar por essa estrutura de dados::

 soup.title
 # <title>A história do Arganaz</title>

 soup.title.name
 # u'title'

 soup.title.string
 # u'A história do Arganaz'

 soup.title.parent.name
 # u'head'

 soup.p
 # <p class="title"><b>A história do Arganaz</b></p>

 soup.p['class']
 # u'title'

 soup.a
 # <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>

 soup.find_all('a')
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
 #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
 #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

 soup.find(id="link3")
 # <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>

Uma tarefa comum é extrair todos os URLs encontrados nas tags <a> de uma página::

 for link in soup.find_all('a'):
     print(link.get('href'))
 # http://example.com/elsie
 # http://example.com/lacie
 # http://example.com/tillie

Outra tarefa comum é extrair todo o texto de uma página::

 print(soup.get_text())
 # A história do Arganaz
 #
 # A história do Arganaz
 #
 # Era uma vez três irmãzinhas; e seus nomes eram
 # Elsie,
 # Lacie e
 # Tillie;
 # e elas viviam no fundo de um poço.
 #
 # ...

Isso parece com o que você precisa? Se assim for, continue a ler.

Instalando o Beautiful Soup
=========================

Se você está usando uma versão recente do Debian ou Ubuntu Linux, você pode
instalar o Beautiful Soup com o gerenciador de pacotes do sistema:

:kbd:`$ apt-get install python-bs4` (para o Python 2)

:kbd:`$ apt-get install python3-bs4` (para o Python 3)

Beautiful Soup 4 é publicado através do PyPi, então se você não puder instalá-lo
com o empacotador do sistema, você pode instalá-lo com ``easy_install`` ou
``pip``. O nome do pacote é ``beautifulsoup4`` e o mesmo pacote
funciona em Python 2 e Python 3. Certifique-se de usar a versão correta do
``pip`` ou ``easy_install`` para sua versão em Python (estes podem ser nomeados
``pip3`` e ``easy_install3`` respectivamente se você estiver usando o Python 3).

:kbd:`$ easy_install beautifulsoup4`

:kbd:`$ pip install beautifulsoup4`

(O pacote ``BeautifulSoup`` provavelmente `não` é o que você quer. Este é
o lançamento principal anterior, `Beautiful Soup 3`_. Muitos softwares usam
BS3, então ainda está disponível, mas se você está escrevendo um novo código você
deve instalar o ``beautifulsoup4``.)

Se você não tem ``easy_install`` ou ``pip`` instalados, você pode
`baixar o tarball fonte do Beautiful Soup 4
<http://www.crummy.com/software/BeautifulSoup/download/4.x/>`_ e
o instalar com o ``setup.py``.

:kbd:`$ python setup.py install`

Se tudo mais falhar, a licença para Beautiful Soup permite que você
empacote toda a biblioteca com o seu aplicativo. Você pode baixar o
tarball, copiar seu diretório ``bs4`` na base de código do seu aplicativo,
e usar o Beautiful Soup sem precisar instalá-lo.

Eu uso Python 2.7 e Python 3.2 para desenvolver o Beautiful Soup, mas
deve funcionar com outras versões recentes.

Problemas depois da instalação
---------------------------

Beautiful Soup é empacotado como código Python 2. Quando você instala para
usar com o Python 3, ele é automaticamente convertido para o código do Python 3. E se
você não instala o pacote, o código não será convertido. Há
também relatórios em máquinas Windows da versão errada sendo
instalada.

Se você obtiver o erro ``ImportError`` "No module named HTMLParser", o seu
problema é que você está executando a versão Python 2 do código em
Python 3.

Se você obtiver o erro ``ImportError`` "No module named html.parser", o seu
problema é que você está executando a versão Python 3 do código em
Python 2.

Em ambos os casos, sua melhor aposta é remover completamente a instalação
do Beautiful Soup do seu sistema (incluindo qualquer diretório criado
quando você descompactou o tarball) e tentar a instalação novamente.

Se você obtiver o erro ``SyntaxError`` "Invalid syntax" na linha
``ROOT_TAG_NAME = u'[document]'``, você precisa converter o código
Python 2 para Python 3. Você pode fazer isso instalando o pacote:

:kbd:`$ python3 setup.py install`

ou executando manualmente o script de conversão ``2to3`` do Python no
diretório ``bs4``:

:kbd:`$ 2to3-3.2 -w bs4`

.. _instalacao-de-parser:


Instalando um parser
-------------------

Beautiful Soup suporta o parser HTML incluído na biblioteca padrão do
Python, mas também suporta certos parsers Python de terceiros.
Um é o parser `lxml<http://lxml.de/>`_. Dependendo da sua configuração,
você pode instalar o lxml com um destes comandos:

:kbd:`$ apt-get install python-lxml`

:kbd:`$ easy_install lxml`

:kbd:`$ pip install lxml`

Outra alternativa é o parser `html5lib <http://code.google.com/p/html5lib/>`_, 
que faz o parsing do HTML do jeito que o web browser faz. Dependendo
da sua configuração, você pode instalar o html5lib
com um desses comandos:

:kbd:`$ apt-get install python-html5lib`

:kbd:`$ easy_install html5lib`

:kbd:`$ pip install html5lib`

Essa tabela sumariza as vantagens e as desvantagens de cada biblioteca de parsing:

+----------------------+--------------------------------------------+--------------------------------+---------------------------+
| Parser               | Uso comum                                  | Vantagens                      | Desvantagens              |
+----------------------+--------------------------------------------+--------------------------------+---------------------------+
|html.parser do Python | ``BeautifulSoup(markup, "html.parser")``   | * Pilhas incluídas             | * Não muito leniente      |
|                      |                                            | * Velocidade razoável          |   (antes do Python 2.7.3  |
|                      |                                            | * Leniente(como no Python 2.7.3|   ou 3.2.2)               |
|                      |                                            |   e 3.2.)                      |                           |
+----------------------+--------------------------------------------+--------------------------------+---------------------------+
| parser HTML do lxml  | ``BeautifulSoup(markup, "lxml")``          | * Muito Rápido                 | * Dependência externa do C|
|                      |                                            | * Leniente                     |                           |
+----------------------+--------------------------------------------+--------------------------------+---------------------------+
| parser XML do lxml   | ``BeautifulSoup(markup, "lxml-xml")``      | * Muito Rápido                 | * Dependência externa do C|
|                      | ``BeautifulSoup(markup, "xml")``           | * Atualmente o único parser XML|                           |
|                      |                                            |   suportado                    |                           |
+----------------------+--------------------------------------------+--------------------------------+---------------------------+
|      html5lib        | ``BeautifulSoup(markup, "html5lib")``      | * Extremamente leniente        | * Muito lento             |
|                      |                                            | * Parsing de páginas da mesma  | * Dependência externa     |
|                      |                                            |   forma que um web browser     |   do Python               |
|                      |                                            | * Cria um HTML5 válido         |                           |
+----------------------+--------------------------------------------+--------------------------------+---------------------------+

Se você puder, eu recomendo que você instale e use lxml pela velocidade. Se você
está usando uma versão do Python 2 anterior à 2.7.3, ou uma versão do Python
3 anterior à 3.2.2, é `essencial` que você instale lxml ou
html5lib--O parser HTML do Python não é muito bom em versões mais antigas.

Observe que, se um documento for inválido, diferentes parsers gerarão
diferentes árvores do Beautiful Soup para isso. Veja `Diferenças
entre parsers`_ para detalhes.

Fazendo o soup
==============

Para analisar um documento, passe-o para o construtor
``BeautifulSoup``. Você pode passar uma string de texto ou um arquivo aberto::

 from bs4 import BeautifulSoup

 with open("index.html") as fp:
     soup = BeautifulSoup(fp)

 soup = BeautifulSoup("<html>data</html>")

Primeiramente, o documento é convertido para Unicode e as entidades HTML são
convertidas em caracteres Unicode::

 BeautifulSoup("Sacr&eacute; bleu!")
 <html><head></head><body>Sacré bleu!</body></html>

Beautiful Soup então analisa o documento usando o melhor analisador
disponível. Um analisador HTML será utilizado por padrão a não ser que você especifique
a utilização de um analisador XML. (Consulte `Analisando XML`_.)

Tipos de objetos
================

Beautiful Soup transforma um documento HTML complexo em uma árvore complexa
de objetos Python. Mas você só terá que lidar com quatro
`tipos` de objetos:``Tag``,``NavigableString``,``BeautifulSoup``,
e ``Comment``.

.. _Tag:

``Tag``
-------

Um objeto ``Tag`` corresponde a uma tag XML ou HTML no documento original::

  soup = BeautifulSoup ('<b class = "bold"> Extremely bold </ b>')
  tag = soup.b
  tipo (tag)
  # <class 'bs4.element.Tag'>

Tags tem muitos atributos e métodos, e eu cobrirei a maioria deles
em `Navegando a árvore`_ e `Procurando na árvore`_. Por enquanto, as mais
importantes características de uma tag são seu nome e atributos.

Nome
^^^^

Cada tag tem um nome, accessível como ``.name``::

 tag.name
 # u'b'

Se você alterar o nome de uma tag, a alteração será refletida em qualquer HTML
marcação gerada pelo Beautiful Soup::

 tag.name = "blockquote"
 tag
 # <blockquote class="boldest">Extremely bold</blockquote>

Atributos
^^^^^^^^^^

Uma tag pode ter qualquer número de atributos. A tag ``<b
id = "bold">`` tem um atributo "id" cujo valor é
"negrito". Você pode acessar os atributos de uma tag tratando a tag como
um dicionário::

  tag ['id']
  # u'boldest '

Você pode acessar o dicionário diretamente como ``.attrs``::

  tag.attrs
  # {u'id ':'boldest'}

Você pode adicionar, remover e modificar os atributos de uma tag. Mais uma vez, isso é
feito tratando a tag como um dicionário::

 tag['id'] = 'verybold'
 tag['another-attribute'] = 1
 tag
 # <b another-attribute="1" id="verybold"></b>

 del tag['id']
 del tag['another-attribute']
 tag
 # <b></b>

 tag['id']
 # KeyError: 'id'
 print(tag.get('id'))
 # None

.. _multivalue:

Atributos de valor múltiplo
&&&&&&&&&&&&&&&&&&&&&&&

O HTML 4 define alguns atributos que podem ter vários valores. HTML 5
remove alguns deles, mas define alguns mais. O mais comum
atributo de valores múltiplos é ``class`` (isto é, uma tag pode ter mais que
uma classe CSS). Outros incluem ``rel``, ``rev``, ``accept-charset``,
``headers`` e ``accesskey``. Beautiful Soup apresenta o(s) valor(es)
de um atributo com vários valores como uma lista::

 css_soup = BeautifulSoup('<p class="body"></p>')
 css_soup.p['class']
 # ["body"]

 css_soup = BeautifulSoup('<p class="body strikeout"></p>')
 css_soup.p['class']
 # ["body", "strikeout"]

Se um atributo `parece` ter mais de um valor, mas não é
um atributo com valores múltiplos, conforme definido por qualquer versão do padrão
HTML, Beautiful Soup irá ignorar o atributo::

 id_soup = BeautifulSoup('<p id="my id"></p>')
 id_soup.p['id']
 # 'my id'
 
Quando você transforma uma tag de volta para uma string, vários valores 
de atributo são consolidados::

 rel_soup = BeautifulSoup('<p>Back to the <a rel="index">homepage</a></p>')
 rel_soup.a['rel']
 # ['index']
 rel_soup.a['rel'] = ['index', 'contents']
 print(rel_soup.p)
 # <p>Back to the <a rel="index contents">homepage</a></p>

Você pode usar ```get_attribute_list`` para obter um valor que seja sempre uma lista,
string, seja ou não um atributo de valor múltiplo

  id_soup.p.get_attribute_list('id')
  # ["my id"]

Se você fizer o parsing de um documento como XML, não há atributos de valor múltiplo::

 xml_soup = BeautifulSoup('<p class="body strikeout"></p>', 'xml')
 xml_soup.p['class']
 # u'body strikeout'


``NavigableString``
-------------------

Uma string corresponde a um bit de texto dentro de uma tag. Beautiful Soup
usa a classe ``NavigableString`` para conter esses bits de texto::

 tag.string
 # u'Extremely bold'
 type(tag.string)
 # <class 'bs4.element.NavigableString'>
 
Um ``NavigableString`` é como uma string Unicode do Python, exceto
que também suporta algumas das características descritas em `Navegando pela árvore`_ e
`Procurando na árvore`_. Você pode converter um ``NavigableString`` 
para uma string Unicode com ``unicode ()``::

 unicode_string = unicode(tag.string)
 unicode_string
 # u'Extremely bold'
 type(unicode_string)
 # <type 'unicode'>

Você não pode editar uma string, mas pode substituir uma string por
outra, usando :ref:`replace_with`::

 tag.string.replace_with("No longer bold")
 tag
 # <blockquote>No longer bold</blockquote>

O ``NavigableString`` suporta a maioria dos recursos descritos em
`Navegando pela árvore`_ e `Procurando na árvore`_, mas não em todos
eles. Em particular, uma vez que uma string não pode conter nada (da mesma maneira 
que uma tag pode conter uma string ou outra tag), strings não suportam
atributos ``.contents`` ou ``.string``, ou o método ``find ()``.

Se você quiser usar um ``NavigableString`` fora do Beautiful Soup,
você deve chamar ``unicode ()`` para transformá-lo em uma
sequência normal de caracteres Unicode Python. Se você não fizer isso, sua string 
carregará uma referência a toda a árvore de parsing do Beautifulsoup, mesmo quando você 
terminar de usar o Beautiful Soup. Isso é um grande desperdício de memória.

``BeautifulSoup``
-----------------

Since the ``BeautifulSoup`` object doesn't correspond to an actual
HTML or XML tag, it has no name and no attributes. But sometimes it's
useful to look at its ``.name``, so it's been given the special
``.name`` "[document]"::

O objeto ``BeautifulSoup`` em si representa o documento como um
todo. Para a maioria dos propósitos, você pode tratá-lo como um objeto
:ref:`Tag`. Isso significa que ele suporta a maioria dos métodos descritos em
`Navegando na árvore`_ e `Procurando na árvore`_.

Já que o objeto ``BeautifulSoup`` não corresponde a um
HTML ou Tag XML, ele não tem nome nem atributos. Mas às vezes é
é útil olhar para o seu ``.name``, então foi dado a ele o especial
``.name`` "[document]"::

 soup.name
 # u'[document]'

Comments e outras strings especiais
---------------------------------------

``Tag``, ``NavigableString`` e ``BeautifulSoup`` cobrem quase
tudo o que você verá em um arquivo HTML ou XML, mas há alguns
pedaços restantes. O único que você provavelmente precisará se preocupar
é o comentário::

 markup = "<b><!--Ei, amigo. Quer comprar um parser usado?--></b>"
 soup = BeautifulSoup(markup)
 comment = soup.b.string
 type(comment)
 # <class 'bs4.element.Comment'>

O objeto ``Comment`` é apenas um tipo especial de ``NavigableString``::

 comment
 # u'Ei, amigo. Quer comprar um parser usado?'
 
Mas quando aparece como parte de um documento HTML, um ``Comment`` é
exibido com formatação especial::

 print(soup.b.prettify())
 # <b>
 #  <!--Ei, amigo. Quer comprar um parser usado?-->
 # </b>

Beautiful Soup define classes para qualquer outra coisa que possa aparecer em
um documento XML: ``CData``, ``ProcessingInstruction``,
``Declaration`` e ``Doctype``. Assim como ``Comment``, essas classes
são subclasses de ``NavigableString`` que adicionam algo extra à
string. Aqui está um exemplo que substitui o comentário por um CDATA
block::

 from bs4 import CData
 cdata = CData("A CDATA block")
 comment.replace_with(cdata)

 print(soup.b.prettify())
 # <b>
 #  <![CDATA[A CDATA block]]>
 # </b>


Navegando pela árvore
===================

Aqui está o documento HTML "Three sisters" mais uma vez::

 html_doc = """
 <html><head><title>A história do Arganaz</title></head>
 <body>
 <p class="title"><b>A história do Arganaz</b></p>

 <p class="story">Era uma vez três irmãzinhas; e seus nomes eram
 <a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
 <a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> e
 <a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
 e elas viviam no fundo de um poço.</p>

 <p class="story">...</p>
 """

 from bs4 import BeautifulSoup
 soup = BeautifulSoup(html_doc, 'html.parser')
 
Vou usar isso como um exemplo para mostrar a você como se mover de uma parte
de um documento para outro.

Descendo
----------

Tags podem conter strings e outras tags. Esses elementos são
`filhos` da tag. Beautiful Soup fornece muitos atributos diferentes para
navegar e iterar sobre os filhos de uma tag.

Observe que as strings do Beautiful Soup não suportam nenhum dessas
atributos, porque uma string não pode ter filhos.

Navegando usando nomes de tags
^^^^^^^^^^^^^^^^^^^^^^^^^^

A maneira mais simples de navegar pela árvore é dizer o nome da
tag que você quer. Se você quiser a tag <head>, apenas diga ``soup.head``::

 soup.head
 # <head><title>A história do Arganaz</title></head>

 soup.title
 # <title>A história do Arganaz</title>
 
Você pode usar esse truque de novo e de novo para ampliar uma determinada parte
da árvore. Este código obtém a primeira tag <b> abaixo da tag <body>::

 soup.body.b
 # <b>A história do Arganaz</b>

Using a tag name as an attribute will give you only the `first` tag by that
name::

Usar um nome de tag como um atributo lhe dará apenas `primeira` tag por esse
nome::

 soup.a
 # <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>

Se você precisa pegar 'todas' as tags <a>, ou qualquer coisa mais complicada
que a primeira tag com um certo nome, você precisará usar um dos
métodos descritos em `Procurando na árvore`_, como `find_all ()`::

 soup.find_all('a')
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
 #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
 #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

``.contents`` and ``.children``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Os filhos de uma tag estão disponíveis em uma lista chamada ``.contents``::

 head_tag = soup.head
 head_tag
 # <head><title>A história do Arganaz</title></head>

 head_tag.contents
 [<title>A história do Arganaz</title>]

 title_tag = head_tag.contents[0]
 title_tag
 # <title>A história do Arganaz</title>
 title_tag.contents
 # [u'A história do Arganaz']

O objeto ``BeautifulSoup`` em si tem filhos. Neste caso, a
tag <html> é o filho do objeto ``BeautifulSoup``.::

 len(soup.contents)
 # 1
 soup.contents[0].name
 # u'html'

Uma string não tem ``.contents``, porque não pode conter
qualquer coisa::

 text = title_tag.contents[0]
 text.contents
 # AttributeError: 'NavigableString' object has no attribute 'contents'

Instead of getting them as a list, you can iterate over a tag's
children using the ``.children`` generator::

Em vez de obtê-los como uma lista, você pode iterar sobre os filhos
de uma tag usando o gerador ``.children``::

 for child in title_tag.children:
     print(child)
 # A história do Arganaz
``.descendants``
^^^^^^^^^^^^^^^^

Os atributos ``.contents`` e ``.children`` só consideram os filhos
"diretos" de uma tag. Por exemplo, a tag <head> tem uma única criança
direta--a tag <title>::

 head_tag.contents
 # [<title>A história do Arganaz</title>]

Mas a tag <title> tem um filho: a string "A história do Arganaz".
Há um sentido em que essa cadeia também é um filho da
tag <head>. O atributo ``.descendants`` permite iterar sobre `todos`
os filhos de uma tag, recursivamente: seus filhos diretos, os filhos de
seus filhos diretos e assim por diante::

 for child in head_tag.descendants:
     print(child)
 # <title>A história do Arganaz</title>
 # A história do Arganaz
 
A tag <head> tem apenas um filho, mas tem dois descendentes: o
tag <title> e o filho da tag <title>. O objeto ``BeautifulSoup``
só tem um filho direto (a tag <html>), mas tem um monte de
descendentes::

 len(list(soup.children))
 # 1
 len(list(soup.descendants))
 # 25

.. _.string:

``.string``
^^^^^^^^^^^

Se uma tag tiver apenas um filho e esse filho for um ``NavigableString``,
a criança é disponibilizada como ``.string``::

 title_tag.string
 # u'A história do Arganaz'
 
Se o único filho de uma tag é outra tag, e `essa` tag tem uma
``.string``, então a tag pai é considerada como tendo a mesma
``.string`` como filha::

 head_tag.contents
 # [<title>A história do Arganaz</title>]

 head_tag.string
 # u'A história do Arganaz'

Se uma tag contém mais de uma coisa, então não está claro a que
``.string`` deve se referir, então ``.string`` é definido para ser
``None``::

 print(soup.html.string)
 # None

.. _string-generators:

``.strings`` e ``stripped_strings``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Se há mais de uma coisa dentro de uma tag, você ainda pode ver
apenas as strings. Use o gerador ``.strings``::

 for string in soup.strings:
     print(repr(string))
 # u"A história do Arganaz"
 # u'\n\n'
 # u"A história do Arganaz"
 # u'\n\n'
 # u'Era uma vez três irmãzinhas; e seus nomes eram\n'
 # u'Elsie'
 # u',\n'
 # u'Lacie'
 # u' e\n'
 # u'Tillie'
 # u';\ne elas viviam no fundo de um poço.'
 # u'\n\n'
 # u'...'
 # u'\n'
 
Essas strings tendem a ter muitos espaços em branco extras, o que você pode
remover usando o gerador ``.stripped_strings`` em vez disso::

 for string in soup.stripped_strings:
     print(repr(string))
 # u"A história do Arganaz"
 # u"A história do Arganaz"
 # u'Era uma vez três irmãzinhas; e seus nomes eram'
 # u'Elsie'
 # u','
 # u'Lacie'
 # u'and'
 # u'Tillie'
 # u';\ne elas viviam no fundo de um poço.'
 # u'...'

Aqui, strings consistindo inteiramente de espaço em branco são ignoradas, e
espaços em branco no início e no final das strings são removidos.

Subindo
--------

Continuando com a analogia da "árvore genealógica", toda tag e toda
string tem um `pai`: a tag que a contém.

.. _.parent:

``.parent``
^^^^^^^^^^^

Você pode acessar o pai de um elemento com o atributo ``.parent``. Por
Exemplo no documento "three sisters", a thag <head> é o pai da tag
<title>::

 title_tag = soup.title
 title_tag
 # <title>The Dormouse's story</title>
 title_tag.parent
 # <head><title>The Dormouse's story</title></head>

A própria string título tem um pai: a tag <title> que a contem:
it::

 title_tag.string.parent
 # <title>The Dormouse's story</title>

O pai de uma tag de nível mais alto como <html> é o próprio objeto
``BeautifulSoup``::

 html_tag = soup.html
 type(html_tag.parent)
 # <class 'bs4.BeautifulSoup'>

E o ``.parent`` de um objeto ``BeautifulSoup`` é definido como None::

 print(soup.parent)
 # None

.. _.parents:

``.parents``
^^^^^^^^^^^^

Você pode iterar por todos os elementos pais com 
``.parents``. Este exemplo usa ``.parents`` para viajar de uma tag <a>
enterrada no interior do documento, para o topo do documento::

 link = soup.a
 link
 # <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>
 for parent in link.parents:
     if parent is None:
         print(parent)
     else:
         print(parent.name)
 # p
 # body
 # html
 # [document]
 # None

Going sideways
--------------

Consider a simple document like this::

 sibling_soup = BeautifulSoup("<a><b>text1</b><c>text2</c></b></a>")
 print(sibling_soup.prettify())
 # <html>
 #  <body>
 #   <a>
 #    <b>
 #     text1
 #    </b>
 #    <c>
 #     text2
 #    </c>
 #   </a>
 #  </body>
 # </html>

The <b> tag and the <c> tag are at the same level: they're both direct
children of the same tag. We call them `siblings`. When a document is
pretty-printed, siblings show up at the same indentation level. You
can also use this relationship in the code you write.

``.next_sibling`` and ``.previous_sibling``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can use ``.next_sibling`` and ``.previous_sibling`` to navigate
between page elements that are on the same level of the parse tree::

 sibling_soup.b.next_sibling
 # <c>text2</c>

 sibling_soup.c.previous_sibling
 # <b>text1</b>

The <b> tag has a ``.next_sibling``, but no ``.previous_sibling``,
because there's nothing before the <b> tag `on the same level of the
tree`. For the same reason, the <c> tag has a ``.previous_sibling``
but no ``.next_sibling``::

 print(sibling_soup.b.previous_sibling)
 # None
 print(sibling_soup.c.next_sibling)
 # None

The strings "text1" and "text2" are `not` siblings, because they don't
have the same parent::

 sibling_soup.b.string
 # u'text1'

 print(sibling_soup.b.string.next_sibling)
 # None

In real documents, the ``.next_sibling`` or ``.previous_sibling`` of a
tag will usually be a string containing whitespace. Going back to the
"three sisters" document::

 <a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>
 <a href="http://example.com/lacie" class="sister" id="link2">Lacie</a>
 <a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>

You might think that the ``.next_sibling`` of the first <a> tag would
be the second <a> tag. But actually, it's a string: the comma and
newline that separate the first <a> tag from the second::

 link = soup.a
 link
 # <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>

 link.next_sibling
 # u',\n'

The second <a> tag is actually the ``.next_sibling`` of the comma::

 link.next_sibling.next_sibling
 # <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>

.. _sibling-generators:

``.next_siblings`` and ``.previous_siblings``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can iterate over a tag's siblings with ``.next_siblings`` or
``.previous_siblings``::

 for sibling in soup.a.next_siblings:
     print(repr(sibling))
 # u',\n'
 # <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>
 # u' and\n'
 # <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>
 # u'; and they lived at the bottom of a well.'
 # None

 for sibling in soup.find(id="link3").previous_siblings:
     print(repr(sibling))
 # ' and\n'
 # <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>
 # u',\n'
 # <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>
 # u'Once upon a time there were three little sisters; and their names were\n'
 # None

Going back and forth
--------------------

Take a look at the beginning of the "three sisters" document::

 <html><head><title>The Dormouse's story</title></head>
 <p class="title"><b>The Dormouse's story</b></p>

An HTML parser takes this string of characters and turns it into a
series of events: "open an <html> tag", "open a <head> tag", "open a
<title> tag", "add a string", "close the <title> tag", "open a <p>
tag", and so on. Beautiful Soup offers tools for reconstructing the
initial parse of the document.

.. _element-generators:

``.next_element`` and ``.previous_element``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``.next_element`` attribute of a string or tag points to whatever
was parsed immediately afterwards. It might be the same as
``.next_sibling``, but it's usually drastically different.

Here's the final <a> tag in the "three sisters" document. Its
``.next_sibling`` is a string: the conclusion of the sentence that was
interrupted by the start of the <a> tag.::

 last_a_tag = soup.find("a", id="link3")
 last_a_tag
 # <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>

 last_a_tag.next_sibling
 # '; and they lived at the bottom of a well.'

But the ``.next_element`` of that <a> tag, the thing that was parsed
immediately after the <a> tag, is `not` the rest of that sentence:
it's the word "Tillie"::

 last_a_tag.next_element
 # u'Tillie'

That's because in the original markup, the word "Tillie" appeared
before that semicolon. The parser encountered an <a> tag, then the
word "Tillie", then the closing </a> tag, then the semicolon and rest of
the sentence. The semicolon is on the same level as the <a> tag, but the
word "Tillie" was encountered first.

The ``.previous_element`` attribute is the exact opposite of
``.next_element``. It points to whatever element was parsed
immediately before this one::

 last_a_tag.previous_element
 # u' and\n'
 last_a_tag.previous_element.next_element
 # <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>

``.next_elements`` e ``.previous_elements``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A essa altura, você deve ter pego a idéia. É possível usar esses iteradores para percorrer
o documento a medida que ele é parseado.::

 for element in last_a_tag.next_elements:
     print(repr(element))
 # u'Tillie'
 # u';\nand they lived at the bottom of a well.'
 # u'\n\n'
 # <p class="story">...</p>
 # u'...'
 # u'\n'
 # None

Percorrendo a árvore
====================

Beautiful Soup possui muitos métodos para percorrer a árvore de elementos,
no entanto, eles são  muito parecidos. Eu vou focar a maior parte do tempo explicando
os dois métodos mais populares: ``find()`` e ``find_all()``. Os outros
métodos recebem praticamente os mesmos argumentos e eu vou
abordá-los brevemente

Mais uma vez, eu vou usar o documento "three sisters" como exemplo::

 html_doc = """
 <html><head><title>The Dormouse's story</title></head>
 <body>
 <p class="title"><b>The Dormouse's story</b></p>

 <p class="story">Once upon a time there were three little sisters; and their names were
 <a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
 <a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
 <a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
 and they lived at the bottom of a well.</p>

 <p class="story">...</p>
 """

 from bs4 import BeautifulSoup
 soup = BeautifulSoup(html_doc, 'html.parser')

Ao utilizar um filtro em um argumento como no ``find_all()``, você pode
acessar partes do documento que você está interessado.

Tipos de Filtros
----------------

Antes de entrar em detalhes sobre o ``find_all()`` e outros métodos parecidos, eu
quero mostrar exemplos de diferentes filtros que você pode passar para esses
métodos. Esse filtros aparecem constantemente em toda a API de busca.
Você pode usá-los para filtrar baseado no nome de uma tag,
on its attributes, on the text of a string, or on some combination of
seus atributos, no texto de uma string, ou usar uma combinação desses tipos.

.. _a string:

Uma string
^^^^^^^^^^

O filtro mais simples é o de uma string. Passe uma string para um método de busca e
Beautiful Soup vai procurar por uma parte de texto que corresponda exatamente aquela string. Esse
trecho de código encontra todas as tags <b> no document::

 soup.find_all('b')
 # [<b>The Dormouse's story</b>]

Se você passar uma string de bytes, Beautiful Soup assume que a string está codificada
como UTF-8. Você pode evitar isso passando uma string Unicode.

.. _a regular expression:

Uma expressão regular
^^^^^^^^^^^^^^^^^^^^^

Se você passar uma expressão regular, Beautiful Soup vai filtrar
em relação a expressão regular usando o método ``search()``. Esse trecho de código
encontrar todas as tags em que o nome comece com "b"; nesse
caso, as tags <body> e <b>::

 import re
 for tag in soup.find_all(re.compile("^b")):
     print(tag.name)
 # body
 # b

Esse trecho de código encontra todas as tags em que o nome contém a letra 't'::

 for tag in soup.find_all(re.compile("t")):
     print(tag.name)
 # html
 # title

.. _a list:

Uma Lista
^^^^^^^^^

Se você passar uma lista, Beautiful Soup executará uma busca por uma string que corresponda
a `qualquer` item da lista. Esse trecho de código, por exemplo, encontra todas as tags <a>
`e` todas as tags <b>::

 soup.find_all(["a", "b"])
 # [<b>The Dormouse's story</b>,
 #  <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
 #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
 #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

.. _the value True:

``True``
^^^^^^^^

O valor ``True`` permite encontrar tudo que for possível. Esse trecho de código encontra `todas`
as tags em um documento, com exceção das strings de texto::

 for tag in soup.find_all(True):
     print(tag.name)
 # html
 # head
 # title
 # body
 # p
 # b
 # p
 # a
 # a
 # a
 # p

.. a function:

Uma função
^^^^^^^^^^

Se nenhuma das opções anteriores funcionar pra você, é possível definir uma função que
recebe um element como único argumento. A função deve retornar
``True`` se o argumento correspondeer e ``False`` caso contrário.

A função abaixo retorna ``True`` se uma tag possui o attributo "class"
e não possui o atributo "id"::

 def has_class_but_no_id(tag):
     return tag.has_attr('class') and not tag.has_attr('id')

Ao executar essa função como argumento em ``find_all()`` você terá acesso a todas as tags <p>::

 soup.find_all(has_class_but_no_id)
 # [<p class="title"><b>The Dormouse's story</b></p>,
 #  <p class="story">Once upon a time there were...</p>,
 #  <p class="story">...</p>]

Essa função retorna somente as tags <p>. Não retorna as tags <a>,
porque elas possuem os atributos "class" e "id" na sua definição. Não retorna
as tags <html> e <title>, porque essas não possuem o atributo "class"
na sua definição.

Se você passar um atributo específico para uma função em um filtro, como
``href`` por exemplo, o argumento passado para a função se torna um atributo,
e não uma tag. No exemplo a seguir, a função retorna todas as tags ``a``
em que o atributo ``href`` *não* corresponde a uma expressão regular::

 def not_lacie(href):
     return href and not re.compile("lacie").search(href)
 soup.find_all(href=not_lacie)
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
 #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

A função pode ser tão complicada quanto você precisar. Nesse outro exemplo,
a função retorna ``True`` se uma tag está cercada de objetos string::

 from bs4 import NavigableString
 def surrounded_by_strings(tag):
     return (isinstance(tag.next_element, NavigableString)
             and isinstance(tag.previous_element, NavigableString))

 for tag in soup.find_all(surrounded_by_strings):
     print tag.name
 # p
 # a
 # a
 # a
 # p

Agora estamos preparados para discutir os métodos de busca em detalhes.

``find_all()``
--------------

Assinatura: find_all(:ref:`name <name>`, :ref:`attrs <attrs>`, :ref:`recursive
<recursive>`, :ref:`string <string>`, :ref:`limit <limit>`, :ref:`**kwargs <kwargs>`)

O método ``find_all()`` busca através dos descendentes de uma tag
e retorna `todos` os descendentes que correspondem aos filtros. Diversos
exemplos estão descritos em `Tipos de Filtros`_, mas outros estão descritos a seguir::

 soup.find_all("title")
 # [<title>The Dormouse's story</title>]

 soup.find_all("p", "title")
 # [<p class="title"><b>The Dormouse's story</b></p>]

 soup.find_all("a")
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
 #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
 #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

 soup.find_all(id="link2")
 # [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]

 import re
 soup.find(string=re.compile("sisters"))
 # u'Once upon a time there were three little sisters; and their names were\n'

Alguns destes podem parecer familiares, mas outros são novos. O que significa
passar uma ``string``, ou um ``id``? Por que ``find_all("p", "title")``
encontra uma tag <p> com a classe CSS "title"?
Vamos discutir os argumentos de ``find_all()``.

.. _name:

The ``name`` argument
^^^^^^^^^^^^^^^^^^^^^

Pass in a value for ``name`` and you'll tell Beautiful Soup to only
consider tags with certain names. Text strings will be ignored, as
will tags whose names that don't match.

This is the simplest usage::

 soup.find_all("title")
 # [<title>The Dormouse's story</title>]

Recall from `Kinds of filters`_ that the value to ``name`` can be `a
string`_, `a regular expression`_, `a list`_, `a function`_, or `the value
True`_.

.. _kwargs:

The keyword arguments
^^^^^^^^^^^^^^^^^^^^^

Any argument that's not recognized will be turned into a filter on one
of a tag's attributes. If you pass in a value for an argument called ``id``,
Beautiful Soup will filter against each tag's 'id' attribute::

 soup.find_all(id='link2')
 # [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]

If you pass in a value for ``href``, Beautiful Soup will filter
against each tag's 'href' attribute::

 soup.find_all(href=re.compile("elsie"))
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>]

You can filter an attribute based on `a string`_, `a regular
expression`_, `a list`_, `a function`_, or `the value True`_.

This code finds all tags whose ``id`` attribute has a value,
regardless of what the value is::

 soup.find_all(id=True)
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
 #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
 #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

You can filter multiple attributes at once by passing in more than one
keyword argument::

 soup.find_all(href=re.compile("elsie"), id='link1')
 # [<a class="sister" href="http://example.com/elsie" id="link1">three</a>]

Some attributes, like the data-* attributes in HTML 5, have names that
can't be used as the names of keyword arguments::

 data_soup = BeautifulSoup('<div data-foo="value">foo!</div>')
 data_soup.find_all(data-foo="value")
 # SyntaxError: keyword can't be an expression

You can use these attributes in searches by putting them into a
dictionary and passing the dictionary into ``find_all()`` as the
``attrs`` argument::

 data_soup.find_all(attrs={"data-foo": "value"})
 # [<div data-foo="value">foo!</div>]

You can't use a keyword argument to search for HTML's 'name' element,
because Beautiful Soup uses the ``name`` argument to contain the name
of the tag itself. Instead, you can give a value to 'name' in the
``attrs`` argument.

 name_soup = BeautifulSoup('<input name="email"/>')
 name_soup.find_all(name="email")
 # []
 name_soup.find_all(attrs={"name": "email"})
 # [<input name="email"/>]

.. _attrs:

Searching by CSS class
^^^^^^^^^^^^^^^^^^^^^^

It's very useful to search for a tag that has a certain CSS class, but
the name of the CSS attribute, "class", is a reserved word in
Python. Using ``class`` as a keyword argument will give you a syntax
error. As of Beautiful Soup 4.1.2, you can search by CSS class using
the keyword argument ``class_``::

 soup.find_all("a", class_="sister")
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
 #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
 #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

As with any keyword argument, you can pass ``class_`` a string, a regular
expression, a function, or ``True``::

 soup.find_all(class_=re.compile("itl"))
 # [<p class="title"><b>The Dormouse's story</b></p>]

 def has_six_characters(css_class):
     return css_class is not None and len(css_class) == 6

 soup.find_all(class_=has_six_characters)
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
 #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
 #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

:ref:`Remember <multivalue>` that a single tag can have multiple
values for its "class" attribute. When you search for a tag that
matches a certain CSS class, you're matching against `any` of its CSS
classes::

 css_soup = BeautifulSoup('<p class="body strikeout"></p>')
 css_soup.find_all("p", class_="strikeout")
 # [<p class="body strikeout"></p>]

 css_soup.find_all("p", class_="body")
 # [<p class="body strikeout"></p>]

You can also search for the exact string value of the ``class`` attribute::

 css_soup.find_all("p", class_="body strikeout")
 # [<p class="body strikeout"></p>]

But searching for variants of the string value won't work::

 css_soup.find_all("p", class_="strikeout body")
 # []

If you want to search for tags that match two or more CSS classes, you
should use a CSS selector::

 css_soup.select("p.strikeout.body")
 # [<p class="body strikeout"></p>]

In older versions of Beautiful Soup, which don't have the ``class_``
shortcut, you can use the ``attrs`` trick mentioned above. Create a
dictionary whose value for "class" is the string (or regular
expression, or whatever) you want to search for::

 soup.find_all("a", attrs={"class": "sister"})
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
 #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
 #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

.. _string:

The ``string`` argument
^^^^^^^^^^^^^^^^^^^^^^^

With ``string`` you can search for strings instead of tags. As with
``name`` and the keyword arguments, you can pass in `a string`_, `a
regular expression`_, `a list`_, `a function`_, or `the value True`_.
Here are some examples::

 soup.find_all(string="Elsie")
 # [u'Elsie']

 soup.find_all(string=["Tillie", "Elsie", "Lacie"])
 # [u'Elsie', u'Lacie', u'Tillie']

 soup.find_all(string=re.compile("Dormouse"))
 [u"The Dormouse's story", u"The Dormouse's story"]

 def is_the_only_string_within_a_tag(s):
     """Return True if this string is the only child of its parent tag."""
     return (s == s.parent.string)

 soup.find_all(string=is_the_only_string_within_a_tag)
 # [u"The Dormouse's story", u"The Dormouse's story", u'Elsie', u'Lacie', u'Tillie', u'...']

Although ``string`` is for finding strings, you can combine it with
arguments that find tags: Beautiful Soup will find all tags whose
``.string`` matches your value for ``string``. This code finds the <a>
tags whose ``.string`` is "Elsie"::

 soup.find_all("a", string="Elsie")
 # [<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>]

The ``string`` argument is new in Beautiful Soup 4.4.0. In earlier
versions it was called ``text``::

 soup.find_all("a", text="Elsie")
 # [<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>]

.. _limit:

The ``limit`` argument
^^^^^^^^^^^^^^^^^^^^^^

``find_all()`` returns all the tags and strings that match your
filters. This can take a while if the document is large. If you don't
need `all` the results, you can pass in a number for ``limit``. This
works just like the LIMIT keyword in SQL. It tells Beautiful Soup to
stop gathering results after it's found a certain number.

There are three links in the "three sisters" document, but this code
only finds the first two::

 soup.find_all("a", limit=2)
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
 #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]

.. _recursive:

The ``recursive`` argument
^^^^^^^^^^^^^^^^^^^^^^^^^^

If you call ``mytag.find_all()``, Beautiful Soup will examine all the
descendants of ``mytag``: its children, its children's children, and
so on. If you only want Beautiful Soup to consider direct children,
you can pass in ``recursive=False``. See the difference here::

 soup.html.find_all("title")
 # [<title>The Dormouse's story</title>]

 soup.html.find_all("title", recursive=False)
 # []

Here's that part of the document::

 <html>
  <head>
   <title>
    The Dormouse's story
   </title>
  </head>
 ...

The <title> tag is beneath the <html> tag, but it's not `directly`
beneath the <html> tag: the <head> tag is in the way. Beautiful Soup
finds the <title> tag when it's allowed to look at all descendants of
the <html> tag, but when ``recursive=False`` restricts it to the
<html> tag's immediate children, it finds nothing.

Beautiful Soup offers a lot of tree-searching methods (covered below),
and they mostly take the same arguments as ``find_all()``: ``name``,
``attrs``, ``string``, ``limit``, and the keyword arguments. But the
``recursive`` argument is different: ``find_all()`` and ``find()`` are
the only methods that support it. Passing ``recursive=False`` into a
method like ``find_parents()`` wouldn't be very useful.

Calling a tag is like calling ``find_all()``
--------------------------------------------

Because ``find_all()`` is the most popular method in the Beautiful
Soup search API, you can use a shortcut for it. If you treat the
``BeautifulSoup`` object or a ``Tag`` object as though it were a
function, then it's the same as calling ``find_all()`` on that
object. These two lines of code are equivalent::

 soup.find_all("a")
 soup("a")

These two lines are also equivalent::

 soup.title.find_all(string=True)
 soup.title(string=True)

``find()``
----------

Assinatura de tipo: find(:ref:`name <name>`, :ref:`attrs <attrs>`, :ref:`recursive
<recursive>`, :ref:`string <string>`, :ref:`**kwargs <kwargs>`)

O método ``find_all ()`` varre todo o documento procurando
resultados, mas às vezes você quer encontrar apenas um deles. Se você sabe que um
documento só tem uma tag <body>, seria perda de tempo varrer o
documento inteiro procurando por mais. Em vez de passar em ``limit = 1``
toda vez que você chama ``find_all``, você pode usar o método ``find ()``. 
Estas duas linhas de código são quase equivalentes::
 
 soup.find_all('title', limit=1)
 # [<title>A história do Arganaz</title>]

 soup.find('title')
 # <title>A história do Arganaz</title>

A única diferença é que ``find_all()`` retorna uma lista contendo o
resultado, e ``find()`` retorna apenas o resultado.

Caso ``find_all()`` não encontre nada, retorna uma lista vazia. Se
``find()`` não encontra nada, retorna ``None``::

 print(soup.find("nosuchtag"))
 # None

Lembra do truque ``soup.head.title`` de `Navegando usando nomes de
tags`? Esse truque funciona quando ``find()`` é chamado repetidamente::

 soup.head.title
 # <title>A história do Arganaz</title>

 soup.find("head").find("title")
 # <title>A história do Arganaz</title>

``find_parents()`` and ``find_parent()``
----------------------------------------

Assinatura: find_parents(:ref:`name <name>`, :ref:`attrs <attrs>`, :ref:`string <string>`, :ref:`limit <limit>`, :ref:`**kwargs <kwargs>`)

Assinatura: find_parent(:ref:`name <name>`, :ref:`attrs <attrs>`, :ref:`string <string>`, :ref:`**kwargs <kwargs>`)

Eu passei muito tempo falando sobre ``find_all()`` e
``find()``. O Beautiful Soup API define dez outros métodos de
busca em árvore, mas não se assuste. Cinco desses métodos são
basicamente semelhantes ao ``find_all()``, e os outros cinco são basicamente
semelhantes ao ``find()``. As únicas diferenças são em quais partes da
árvore são feitas as buscas.

Primeiro, consideraremos ``find_parents()`` e
``find_parent()``. Lembre-se que ``find_all()`` e ``find()`` percorrem
de forma decrescente a árvore, procurando pelos descendentes da `tag`(marca). Já esses métodos
fazem o oposto: eles percorrem de forma crescente a árvore, procurando pelos pais da `tag`
(ou da `string`). Vamos testá-los, começando da `string`
mais interna no documento das "três irmãs"::

  a_string = soup.find(string="Lacie")
  a_string
  # u'Lacie'

  a_string.find_parents("a")
  # [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]

  a_string.find_parent("p")
  # <p class="story">Once upon a time there were three little sisters; and their names were
  #  <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
  #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a> and
  #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>;
  #  and they lived at the bottom of a well.</p>

  a_string.find_parents("p", class="title")
  # []

Uma das três `tags` <a> é pai direto da `string` em
questão, logo nossa busca a encontra. Uma das três `tags` <p> é
pai indireto da `string`, e nossa busca a encontra
também. Existe uma `tag` <p> de classe CSS "title" `em algum lugar` no
documento, mas não é nenhum dos pais da `string`, então não podemos encontrá-la
com ``find_parents()``.

Talvez você tenha feito a conexão entre ``find_parent()`` e
``find_parents()``, e os atributos `.parent`_ e `.parents`_ 
mencionados anteriormente. A conexão é bastante forte. Esses métodos de busca
atualmente usam ``.parents`` para iteragir sobre todos os pais, e checar
cada um contra o filtro fornecido para verificar se combinam.

``find_next_siblings()`` and ``find_next_sibling()``
----------------------------------------------------

Signature: find_next_siblings(:ref:`name <name>`, :ref:`attrs <attrs>`, :ref:`string <string>`, :ref:`limit <limit>`, :ref:`**kwargs <kwargs>`)

Signature: find_next_sibling(:ref:`name <name>`, :ref:`attrs <attrs>`, :ref:`string <string>`, :ref:`**kwargs <kwargs>`)

These methods use :ref:`.next_siblings <sibling-generators>` to
iterate over the rest of an element's siblings in the tree. The
``find_next_siblings()`` method returns all the siblings that match,
and ``find_next_sibling()`` only returns the first one::

 first_link = soup.a
 first_link
 # <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>

 first_link.find_next_siblings("a")
 # [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
 #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

 first_story_paragraph = soup.find("p", "story")
 first_story_paragraph.find_next_sibling("p")
 # <p class="story">...</p>

``find_previous_siblings()`` and ``find_previous_sibling()``
------------------------------------------------------------

Signature: find_previous_siblings(:ref:`name <name>`, :ref:`attrs <attrs>`, :ref:`string <string>`, :ref:`limit <limit>`, :ref:`**kwargs <kwargs>`)

Signature: find_previous_sibling(:ref:`name <name>`, :ref:`attrs <attrs>`, :ref:`string <string>`, :ref:`**kwargs <kwargs>`)

These methods use :ref:`.previous_siblings <sibling-generators>` to iterate over an element's
siblings that precede it in the tree. The ``find_previous_siblings()``
method returns all the siblings that match, and
``find_previous_sibling()`` only returns the first one::

 last_link = soup.find("a", id="link3")
 last_link
 # <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>

 last_link.find_previous_siblings("a")
 # [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
 #  <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>]

 first_story_paragraph = soup.find("p", "story")
 first_story_paragraph.find_previous_sibling("p")
 # <p class="title"><b>The Dormouse's story</b></p>


``find_all_next()`` and ``find_next()``
---------------------------------------

Signature: find_all_next(:ref:`name <name>`, :ref:`attrs <attrs>`, :ref:`string <string>`, :ref:`limit <limit>`, :ref:`**kwargs <kwargs>`)

Signature: find_next(:ref:`name <name>`, :ref:`attrs <attrs>`, :ref:`string <string>`, :ref:`**kwargs <kwargs>`)

These methods use :ref:`.next_elements <element-generators>` to
iterate over whatever tags and strings that come after it in the
document. The ``find_all_next()`` method returns all matches, and
``find_next()`` only returns the first match::

 first_link = soup.a
 first_link
 # <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>

 first_link.find_all_next(string=True)
 # [u'Elsie', u',\n', u'Lacie', u' and\n', u'Tillie',
 #  u';\nand they lived at the bottom of a well.', u'\n\n', u'...', u'\n']

 first_link.find_next("p")
 # <p class="story">...</p>

In the first example, the string "Elsie" showed up, even though it was
contained within the <a> tag we started from. In the second example,
the last <p> tag in the document showed up, even though it's not in
the same part of the tree as the <a> tag we started from. For these
methods, all that matters is that an element match the filter, and
show up later in the document than the starting element.

``find_all_previous()`` and ``find_previous()``
-----------------------------------------------

Signature: find_all_previous(:ref:`name <name>`, :ref:`attrs <attrs>`, :ref:`string <string>`, :ref:`limit <limit>`, :ref:`**kwargs <kwargs>`)

Signature: find_previous(:ref:`name <name>`, :ref:`attrs <attrs>`, :ref:`string <string>`, :ref:`**kwargs <kwargs>`)

These methods use :ref:`.previous_elements <element-generators>` to
iterate over the tags and strings that came before it in the
document. The ``find_all_previous()`` method returns all matches, and
``find_previous()`` only returns the first match::

 first_link = soup.a
 first_link
 # <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>

 first_link.find_all_previous("p")
 # [<p class="story">Once upon a time there were three little sisters; ...</p>,
 #  <p class="title"><b>The Dormouse's story</b></p>]

 first_link.find_previous("title")
 # <title>The Dormouse's story</title>

The call to ``find_all_previous("p")`` found the first paragraph in
the document (the one with class="title"), but it also finds the
second paragraph, the <p> tag that contains the <a> tag we started
with. This shouldn't be too surprising: we're looking at all the tags
that show up earlier in the document than the one we started with. A
<p> tag that contains an <a> tag must have shown up before the <a>
tag it contains.

CSS selectors
-------------

Beautiful Soup supports the most commonly-used CSS selectors. Just
pass a string into the ``.select()`` method of a ``Tag`` object or the
``BeautifulSoup`` object itself.

You can find tags::

 soup.select("title")
 # [<title>The Dormouse's story</title>]

 soup.select("p:nth-of-type(3)")
 # [<p class="story">...</p>]

Find tags beneath other tags::

 soup.select("body a")
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
 #  <a class="sister" href="http://example.com/lacie"  id="link2">Lacie</a>,
 #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

 soup.select("html head title")
 # [<title>The Dormouse's story</title>]

Find tags `directly` beneath other tags::

 soup.select("head > title")
 # [<title>The Dormouse's story</title>]

 soup.select("p > a")
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
 #  <a class="sister" href="http://example.com/lacie"  id="link2">Lacie</a>,
 #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

 soup.select("p > a:nth-of-type(2)")
 # [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]

 soup.select("p > #link1")
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>]

 soup.select("body > a")
 # []

Find the siblings of tags::

 soup.select("#link1 ~ .sister")
 # [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
 #  <a class="sister" href="http://example.com/tillie"  id="link3">Tillie</a>]

 soup.select("#link1 + .sister")
 # [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]

Find tags by CSS class::

 soup.select(".sister")
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
 #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
 #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

 soup.select("[class~=sister]")
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
 #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
 #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

Find tags by ID::

 soup.select("#link1")
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>]

 soup.select("a#link2")
 # [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]

Find tags that match any selector from a list of selectors:

 soup.select("#link1,#link2")
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
 #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]

Test for the existence of an attribute::

 soup.select('a[href]')
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
 #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
 #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

Find tags by attribute value::

 soup.select('a[href="http://example.com/elsie"]')
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>]

 soup.select('a[href^="http://example.com/"]')
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
 #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
 #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

 soup.select('a[href$="tillie"]')
 # [<a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

 soup.select('a[href*=".com/el"]')
 # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>]

Match language codes::

 multilingual_markup = """
  <p lang="en">Hello</p>
  <p lang="en-us">Howdy, y'all</p>
  <p lang="en-gb">Pip-pip, old fruit</p>
  <p lang="fr">Bonjour mes amis</p>
 """
 multilingual_soup = BeautifulSoup(multilingual_markup)
 multilingual_soup.select('p[lang|=en]')
 # [<p lang="en">Hello</p>,
 #  <p lang="en-us">Howdy, y'all</p>,
 #  <p lang="en-gb">Pip-pip, old fruit</p>]

Find only the first tag that matches a selector::

 soup.select_one(".sister")
 # <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>

This is all a convenience for users who know the CSS selector syntax. You
can do all this stuff with the Beautiful Soup API. And if CSS
selectors are all you need, you might as well use lxml directly: it's
a lot faster, and it supports more CSS selectors. But this lets you
`combine` simple CSS selectors with the Beautiful Soup API.


Modifying the tree
==================

Beautiful Soup's main strength is in searching the parse tree, but you
can also modify the tree and write your changes as a new HTML or XML
document.

Changing tag names and attributes
---------------------------------

I covered this earlier, in `Attributes`_, but it bears repeating. You
can rename a tag, change the values of its attributes, add new
attributes, and delete attributes::

 soup = BeautifulSoup('<b class="boldest">Extremely bold</b>')
 tag = soup.b

 tag.name = "blockquote"
 tag['class'] = 'verybold'
 tag['id'] = 1
 tag
 # <blockquote class="verybold" id="1">Extremely bold</blockquote>

 del tag['class']
 del tag['id']
 tag
 # <blockquote>Extremely bold</blockquote>

Modifying ``.string``
---------------------

If you set a tag's ``.string`` attribute, the tag's contents are
replaced with the string you give::

  markup = '<a href="http://example.com/">I linked to <i>example.com</i></a>'
  soup = BeautifulSoup(markup)

  tag = soup.a
  tag.string = "New link text."
  tag
  # <a href="http://example.com/">New link text.</a>

Be careful: if the tag contained other tags, they and all their
contents will be destroyed.

``append()``
------------

You can add to a tag's contents with ``Tag.append()``. It works just
like calling ``.append()`` on a Python list::

   soup = BeautifulSoup("<a>Foo</a>")
   soup.a.append("Bar")

   soup
   # <html><head></head><body><a>FooBar</a></body></html>
   soup.a.contents
   # [u'Foo', u'Bar']

``NavigableString()`` and ``.new_tag()``
-------------------------------------------------

If you need to add a string to a document, no problem--you can pass a
Python string in to ``append()``, or you can call the ``NavigableString``
constructor::

   soup = BeautifulSoup("<b></b>")
   tag = soup.b
   tag.append("Hello")
   new_string = NavigableString(" there")
   tag.append(new_string)
   tag
   # <b>Hello there.</b>
   tag.contents
   # [u'Hello', u' there']

If you want to create a comment or some other subclass of
``NavigableString``, just call the constructor::

   from bs4 import Comment
   new_comment = Comment("Nice to see you.")
   tag.append(new_comment)
   tag
   # <b>Hello there<!--Nice to see you.--></b>
   tag.contents
   # [u'Hello', u' there', u'Nice to see you.']

(This is a new feature in Beautiful Soup 4.4.0.)

What if you need to create a whole new tag?  The best solution is to
call the factory method ``BeautifulSoup.new_tag()``::

   soup = BeautifulSoup("<b></b>")
   original_tag = soup.b

   new_tag = soup.new_tag("a", href="http://www.example.com")
   original_tag.append(new_tag)
   original_tag
   # <b><a href="http://www.example.com"></a></b>

   new_tag.string = "Link text."
   original_tag
   # <b><a href="http://www.example.com">Link text.</a></b>

Only the first argument, the tag name, is required.

``insert()``
------------

``Tag.insert()`` is just like ``Tag.append()``, except the new element
doesn't necessarily go at the end of its parent's
``.contents``. It'll be inserted at whatever numeric position you
say. It works just like ``.insert()`` on a Python list::

  markup = '<a href="http://example.com/">I linked to <i>example.com</i></a>'
  soup = BeautifulSoup(markup)
  tag = soup.a

  tag.insert(1, "but did not endorse ")
  tag
  # <a href="http://example.com/">I linked to but did not endorse <i>example.com</i></a>
  tag.contents
  # [u'I linked to ', u'but did not endorse', <i>example.com</i>]

``insert_before()`` and ``insert_after()``
------------------------------------------

The ``insert_before()`` method inserts a tag or string immediately
before something else in the parse tree::

   soup = BeautifulSoup("<b>stop</b>")
   tag = soup.new_tag("i")
   tag.string = "Don't"
   soup.b.string.insert_before(tag)
   soup.b
   # <b><i>Don't</i>stop</b>

The ``insert_after()`` method moves a tag or string so that it
immediately follows something else in the parse tree::

   soup.b.i.insert_after(soup.new_string(" ever "))
   soup.b
   # <b><i>Don't</i> ever stop</b>
   soup.b.contents
   # [<i>Don't</i>, u' ever ', u'stop']

``clear()``
-----------

``Tag.clear()`` remove o conteúdo de uma tag::

  markup = '<a href="http://example.com/">I linked to <i>example.com</i></a>'
  soup = BeautifulSoup(markup)
  tag = soup.a

  tag.clear()
  tag
  # <a href="http://example.com/"></a>

``extract()``
-------------

``PageElement.extract()`` remove uma tag ou string da árvore. Retorna a tag ou string que foi removida::

  markup = '<a href="http://example.com/">I linked to <i>example.com</i></a>'
  soup = BeautifulSoup(markup)
  a_tag = soup.a

  i_tag = soup.i.extract()

  a_tag
  # <a href="http://example.com/">I linked to</a>

  i_tag
  # <i>example.com</i>

  print(i_tag.parent)
  None

Nesta etapa na verdade temos duas árvores analisadas: uma com a raiz no objeto
``BeautifulSoup`` utilizado para analisar o documento, e outra com a raiz
na tag que foi removida. Pode-se continuar e executar ``extract`` no
nó filho do elemento removido::

  my_string = i_tag.string.extract()
  my_string
  # u'example.com'

  print(my_string.parent)
  # None
  i_tag
  # <i></i>


``decompose()``
---------------

``Tag.decompose()`` remove uma tag da árvore, e em seguida, `o destrói
completamente e também seus componentes`::

  markup = '<a href="http://example.com/">I linked to <i>example.com</i></a>'
  soup = BeautifulSoup(markup)
  a_tag = soup.a

  soup.i.decompose()

  a_tag
  # <a href="http://example.com/">I linked to</a>


.. _replace_with:

``replace_with()``
------------------

``PageElement.replace_with()`` remove uma tag ou string da árvore,
e o substitui com a tag ou string desejada::

  markup = '<a href="http://example.com/">I linked to <i>example.com</i></a>'
  soup = BeautifulSoup(markup)
  a_tag = soup.a

  new_tag = soup.new_tag("b")
  new_tag.string = "example.net"
  a_tag.i.replace_with(new_tag)

  a_tag
  # <a href="http://example.com/">I linked to <b>example.net</b></a>

``replace_with()`` retorna a tag ou string substituída, podendo
ser examinada ou adicionada a outra parte da árvore.

``wrap()``
----------

``PageElement.wrap()`` envelopa um elemento na tag especificada.
Retorna o novo envoltório::

 soup = BeautifulSoup("<p>I wish I was bold.</p>")
 soup.p.string.wrap(soup.new_tag("b"))
 # <b>I wish I was bold.</b>

 soup.p.wrap(soup.new_tag("div")
 # <div><p><b>I wish I was bold.</b></p></div>

Este método é novo na Beautiful Soup 4.0.5.

``unwrap()``
---------------------------

``Tag.unwrap()`` é o contrário de ``wrap()``. Substitui uma tag com
qualquer que seja o seu conteúdo. É útil para eliminar marcações::

  markup = '<a href="http://example.com/">I linked to <i>example.com</i></a>'
  soup = BeautifulSoup(markup)
  a_tag = soup.a

  a_tag.i.unwrap()
  a_tag
  # <a href="http://example.com/">I linked to example.com</a>

Tal como ``replace_with()``, ``unwrap()`` retorna a tag
que foi substituída.

Output
======

.. _.prettyprinting:

Pretty-printing
---------------

O método ``prettify()`` converterá uma árvore Beautiful Soup em uma
string formatada em Unicode, com uma tag HTML/XML em sua própria linha::

  markup = '<a href="http://example.com/">I linked to <i>example.com</i></a>'
  soup = BeautifulSoup(markup)
  soup.prettify()
  # '<html>\n <head>\n </head>\n <body>\n  <a href="http://example.com/">\n...'

  print(soup.prettify())
  # <html>
  #  <head>
  #  </head>
  #  <body>
  #   <a href="http://example.com/">
  #    I linked to
  #    <i>
  #     example.com
  #    </i>
  #   </a>
  #  </body>
  # </html>

Você pode executar ``prettify()`` no nível mais alto de um objeto ``BeautifulSoup``,
ou em qualquer de seus objetos do tipo ``Tag``::

  print(soup.a.prettify())
  # <a href="http://example.com/">
  #  I linked to
  #  <i>
  #   example.com
  #  </i>
  # </a>

Non-pretty printing
-------------------

Se apenas uma string, sem formatação, for suficiente, pode-se executar
``unicode()`` ou ``str()`` em um objeto ``BeautifulSoup``, ou em uma
``Tag`` pertencente a ele::

 str(soup)
 # '<html><head></head><body><a href="http://example.com/">I linked to <i>example.com</i></a></body></html>'

 unicode(soup.a)
 # u'<a href="http://example.com/">I linked to <i>example.com</i></a>'

A função ``str()`` retorna uma string codificada em UTF-8. Veja
`Encodings`_ para demais opções.

Pode-se ainda executar ``encode()`` para obter uma bytestring, e ``decode()``
para obter sua versão Unicode.

.. _formatadores_de_saida:

Formatadores de saída
-----------------

Se você der à Beautiful Soup um documento que contenha entidades HTML como
"&lquot;", elas serão convertidas em caracteres Unicode::

 soup = BeautifulSoup("&ldquo;Dammit!&rdquo; he said.")
 unicode(soup)
 # u'<html><head></head><body>\u201cDammit!\u201d he said.</body></html>'

Se você converter o documento em uma string, os caracteres Unicode 
serão codificados como UTF-8. Você não terá as entidades HTML de volta:

 str(soup)
 # '<html><head></head><body>\xe2\x80\x9cDammit!\xe2\x80\x9d he said.</body></html>'

Por padrão, os únicos caracteres que são escapados na saída são "E comercial"
e símbolos de "maior" e "menor" nus. Estes são transformados em "&amp;", "&lt;"
e "&gt;", para que a Beautiful Soup não gere inadvertidamente
HTML ou XML inválidos::

 soup = BeautifulSoup("<p>The law firm of Dewey, Cheatem, & Howe</p>")
 soup.p
 # <p>The law firm of Dewey, Cheatem, &amp; Howe</p>

 soup = BeautifulSoup('<a href="http://example.com/?foo=val1&bar=val2">A link</a>')
 soup.a
 # <a href="http://example.com/?foo=val1&amp;bar=val2">A link</a>

Você pode alterar esse comportamento fornecendo um valor para o 
argumento ``formatter`` para ``prettify()``, ``encode()``, ou
``decode()``. Beautiful Soup reconhece quatro valores possíveis para
``formatter``.

O padrão é ``formatter="minimal"``. As strings só serão processadas
o suficiente para garantir que a Beautiful Soup gere HTML/XML válidos::

 french = "<p>Il a dit &lt;&lt;Sacr&eacute; bleu!&gt;&gt;</p>"
 soup = BeautifulSoup(french)
 print(soup.prettify(formatter="minimal"))
 # <html>
 #  <body>
 #   <p>
 #    Il a dit &lt;&lt;Sacré bleu!&gt;&gt;
 #   </p>
 #  </body>
 # </html>

Se você passar um ``formatter="html"``, Beautiful Soup converterá
Caracteres Unicode para entidades HTML sempre que possível::

 print(soup.prettify(formatter="html"))
 # <html>
 #  <body>
 #   <p>
 #    Il a dit &lt;&lt;Sacr&eacute; bleu!&gt;&gt;
 #   </p>
 #  </body>
 # </html>

Se você passar em ``formatter=None``, Beautiful Soup não irá modificar
strings nas saídas. Esta é a opção mais rápida, porém isso pode levar
à geração de HTML/XML inválidos, como nesses exemplos::

 print(soup.prettify(formatter=None))
 # <html>
 #  <body>
 #   <p>
 #    Il a dit <<Sacré bleu!>>
 #   </p>
 #  </body>
 # </html>

 link_soup = BeautifulSoup('<a href="http://example.com/?foo=val1&bar=val2">A link</a>')
 print(link_soup.a.encode(formatter=None))
 # <a href="http://example.com/?foo=val1&bar=val2">A link</a>

Finalmente, se você passar uma função para ``formatter``, Beautiful Soup
irá chamar essa função uma vez para cada string e valor do atributo no 
documento. Você pode fazer o que quiser nessa função. Aqui está um
formatador que converte strings para maiúsculas e não faz absolutamente
nada mais::

 def uppercase(str):
     return str.upper()

 print(soup.prettify(formatter=uppercase))
 # <html>
 #  <body>
 #   <p>
 #    IL A DIT <<SACRÉ BLEU!>>
 #   </p>
 #  </body>
 # </html>

 print(link_soup.a.prettify(formatter=uppercase))
 # <a href="HTTP://EXAMPLE.COM/?FOO=VAL1&BAR=VAL2">
 #  A LINK
 # </a>

Se você está escrevendo sua própria função, você deveria conhecer a
classe ``EntitySubstitution`` do módulo ``bs4.dammit``. Esta classe
implementa os formatadores padrões da Beautiful Soup como métodos de classe: o
formatador "html" é ``EntitySubstitution.substitute_html`` e o 
formatador "minimal" é ``EntitySubstitution.substitute_xml``. Você pode
utilizar estas funções para simular ``formatter=html`` ou
``formatter=minimal``, e então fazer algo a mais.

Aqui está um exemplo que substitui caracteres Unicode por entidades HTML
sempre que possível, mas `também` converte todas as strings para maiúsculas::

 from bs4.dammit import EntitySubstitution
 def uppercase_and_substitute_html_entities(str):
     return EntitySubstitution.substitute_html(str.upper())

 print(soup.prettify(formatter=uppercase_and_substitute_html_entities))
 # <html>
 #  <body>
 #   <p>
 #    IL A DIT &lt;&lt;SACR&Eacute; BLEU!&gt;&gt;
 #   </p>
 #  </body>
 # </html>

Uma última ressalva: se você criar um objeto ``CData``, o texto dentro
desse objeto é sempre apresentado exatamente como aparece, sem
formatação. Beautiful Soap vai chamar o método formatador, apenas no
caso de você ter escrito um método personalizado que conta todas as strings no
documento ou algo assim, mas vai ignorar o valor de retorno::

 from bs4.element import CData
 soup = BeautifulSoup("<a></a>")
 soup.a.string = CData("one < three")
 print(soup.a.prettify(formatter="xml"))
 # <a>
 #  <![CDATA[one < three]]>
 # </a>


``get_text()``
--------------

Se você quer apenas a parte de texto de um documento ou tag, você pode usar o
método ``get_text()``. Ele retorna todo o texto em um documento ou
abaixo de uma tag, como uma única string Unicode::

  markup = '<a href="http://example.com/">\nI linked to <i>example.com</i>\n</a>'
  soup = BeautifulSoup(markup)

  soup.get_text()
  u'\nI linked to example.com\n'
  soup.i.get_text()
  u'example.com'

Você pode especificar uma string a ser usada para unir os pedaços do texto:

 # soup.get_text("|")
 u'\nI linked to |example.com|\n'

Você pode dizer à Beautiful Soup para retirar o espaço em branco do início e
fim de cada pedaço de texto::

 # soup.get_text("|", strip=True)
 u'I linked to|example.com'

Mas, ao invés, nesse ponto, você pode também usar o :ref:`.stripped_strings <string-generators>`
generator, e processar o texto você mesmo::

 [text for text in soup.stripped_strings]
 # [u'I linked to', u'example.com']

Especificando o parser a ser utilizado
============================

Se você só precisa parsear algum HTML, você pode jogar a marcação no 
construtor ``BeautifulSoup``, e provavelmente dará certo. BeautifulSoup
irá escolher o parser para você e parseará os dados enviados. Mas tem alguns
argumentos adicionais que você passar no construtor para mudar qual parser
é utilizado.

O primeiro argumento no construtor ``BeautifulSoup`` é uma string 
ou arquivos abertos--A marcação que você quer parsear. o segundo 
argumento é `como` você gostaria da marcação parseada.

Se você não especificar nada, você vai obter o melhor parseador HTML
que estiver instalado. Beautiful Soup rankeia o lxml's parser como o
melhor, então html5lib's, então o parseador interno nativo do Python.
Você pode sobrescrever isso especificando algum dos seguintes:

* Que tipo de marcação você quer parsear. Atualmente suportados são
  "html", "xml", and "html5".

* O nome da biblioteca parser que você deseja utilizar.Atualmente suportados
são "lxml", "html5lib", e "html.parser" (parseador interno nativo do Python).

A seção `Instalando um parser`_ contrasta com os parsers suportados.

Se você não possui um parser apropriado instalado, Beautiful Soup irá
ignorar sua requisição e pegar um parser diferente. Atualmente, o unico
parseador XML suportado é lxml. Se você não tem lxml instalado, solicitar
por um parser XML não irá lhe entregar um, e solicitar por "lxml" não
vai funcionar também.

Differences between parsers
---------------------------

Beautiful Soup presents the same interface to a number of different
parsers, but each parser is different. Different parsers will create
different parse trees from the same document. The biggest differences
are between the HTML parsers and the XML parsers. Here's a short
document, parsed as HTML::

 BeautifulSoup("<a><b /></a>")
 # <html><head></head><body><a><b></b></a></body></html>

Since an empty <b /> tag is not valid HTML, the parser turns it into a
<b></b> tag pair.

Here's the same document parsed as XML (running this requires that you
have lxml installed). Note that the empty <b /> tag is left alone, and
that the document is given an XML declaration instead of being put
into an <html> tag.::

 BeautifulSoup("<a><b /></a>", "xml")
 # <?xml version="1.0" encoding="utf-8"?>
 # <a><b/></a>

There are also differences between HTML parsers. If you give Beautiful
Soup a perfectly-formed HTML document, these differences won't
matter. One parser will be faster than another, but they'll all give
you a data structure that looks exactly like the original HTML
document.

But if the document is not perfectly-formed, different parsers will
give different results. Here's a short, invalid document parsed using
lxml's HTML parser. Note that the dangling </p> tag is simply
ignored::

 BeautifulSoup("<a></p>", "lxml")
 # <html><body><a></a></body></html>

Here's the same document parsed using html5lib::

 BeautifulSoup("<a></p>", "html5lib")
 # <html><head></head><body><a><p></p></a></body></html>

Instead of ignoring the dangling </p> tag, html5lib pairs it with an
opening <p> tag. This parser also adds an empty <head> tag to the
document.

Here's the same document parsed with Python's built-in HTML
parser::

 BeautifulSoup("<a></p>", "html.parser")
 # <a></a>

Like html5lib, this parser ignores the closing </p> tag. Unlike
html5lib, this parser makes no attempt to create a well-formed HTML
document by adding a <body> tag. Unlike lxml, it doesn't even bother
to add an <html> tag.

Since the document "<a></p>" is invalid, none of these techniques is
the "correct" way to handle it. The html5lib parser uses techniques
that are part of the HTML5 standard, so it has the best claim on being
the "correct" way, but all three techniques are legitimate.

Differences between parsers can affect your script. If you're planning
on distributing your script to other people, or running it on multiple
machines, you should specify a parser in the ``BeautifulSoup``
constructor. That will reduce the chances that your users parse a
document differently from the way you parse it.

Encodings
=========

Any HTML or XML document is written in a specific encoding like ASCII
or UTF-8.  But when you load that document into Beautiful Soup, you'll
discover it's been converted to Unicode::

 markup = "<h1>Sacr\xc3\xa9 bleu!</h1>"
 soup = BeautifulSoup(markup)
 soup.h1
 # <h1>Sacré bleu!</h1>
 soup.h1.string
 # u'Sacr\xe9 bleu!'

It's not magic. (That sure would be nice.) Beautiful Soup uses a
sub-library called `Unicode, Dammit`_ to detect a document's encoding
and convert it to Unicode. The autodetected encoding is available as
the ``.original_encoding`` attribute of the ``BeautifulSoup`` object::

 soup.original_encoding
 'utf-8'

Unicode, Dammit guesses correctly most of the time, but sometimes it
makes mistakes. Sometimes it guesses correctly, but only after a
byte-by-byte search of the document that takes a very long time. If
you happen to know a document's encoding ahead of time, you can avoid
mistakes and delays by passing it to the ``BeautifulSoup`` constructor
as ``from_encoding``.

Here's a document written in ISO-8859-8. The document is so short that
Unicode, Dammit can't get a good lock on it, and misidentifies it as
ISO-8859-7::

 markup = b"<h1>\xed\xe5\xec\xf9</h1>"
 soup = BeautifulSoup(markup)
 soup.h1
 <h1>νεμω</h1>
 soup.original_encoding
 'ISO-8859-7'

We can fix this by passing in the correct ``from_encoding``::

 soup = BeautifulSoup(markup, from_encoding="iso-8859-8")
 soup.h1
 <h1>םולש</h1>
 soup.original_encoding
 'iso8859-8'

If you don't know what the correct encoding is, but you know that
Unicode, Dammit is guessing wrong, you can pass the wrong guesses in
as ``exclude_encodings``::

 soup = BeautifulSoup(markup, exclude_encodings=["ISO-8859-7"])
 soup.h1
 <h1>םולש</h1>
 soup.original_encoding
 'WINDOWS-1255'

Windows-1255 isn't 100% correct, but that encoding is a compatible
superset of ISO-8859-8, so it's close enough. (``exclude_encodings``
is a new feature in Beautiful Soup 4.4.0.)

In rare cases (usually when a UTF-8 document contains text written in
a completely different encoding), the only way to get Unicode may be
to replace some characters with the special Unicode character
"REPLACEMENT CHARACTER" (U+FFFD, �). If Unicode, Dammit needs to do
this, it will set the ``.contains_replacement_characters`` attribute
to ``True`` on the ``UnicodeDammit`` or ``BeautifulSoup`` object. This
lets you know that the Unicode representation is not an exact
representation of the original--some data was lost. If a document
contains �, but ``.contains_replacement_characters`` is ``False``,
you'll know that the � was there originally (as it is in this
paragraph) and doesn't stand in for missing data.

Output encoding
---------------

When you write out a document from Beautiful Soup, you get a UTF-8
document, even if the document wasn't in UTF-8 to begin with. Here's a
document written in the Latin-1 encoding::

 markup = b'''
  <html>
   <head>
    <meta content="text/html; charset=ISO-Latin-1" http-equiv="Content-type" />
   </head>
   <body>
    <p>Sacr\xe9 bleu!</p>
   </body>
  </html>
 '''

 soup = BeautifulSoup(markup)
 print(soup.prettify())
 # <html>
 #  <head>
 #   <meta content="text/html; charset=utf-8" http-equiv="Content-type" />
 #  </head>
 #  <body>
 #   <p>
 #    Sacré bleu!
 #   </p>
 #  </body>
 # </html>

Note that the <meta> tag has been rewritten to reflect the fact that
the document is now in UTF-8.

If you don't want UTF-8, you can pass an encoding into ``prettify()``::

 print(soup.prettify("latin-1"))
 # <html>
 #  <head>
 #   <meta content="text/html; charset=latin-1" http-equiv="Content-type" />
 # ...

You can also call encode() on the ``BeautifulSoup`` object, or any
element in the soup, just as if it were a Python string::

 soup.p.encode("latin-1")
 # '<p>Sacr\xe9 bleu!</p>'

 soup.p.encode("utf-8")
 # '<p>Sacr\xc3\xa9 bleu!</p>'

Any characters that can't be represented in your chosen encoding will
be converted into numeric XML entity references. Here's a document
that includes the Unicode character SNOWMAN::

 markup = u"<b>\N{SNOWMAN}</b>"
 snowman_soup = BeautifulSoup(markup)
 tag = snowman_soup.b

The SNOWMAN character can be part of a UTF-8 document (it looks like
☃), but there's no representation for that character in ISO-Latin-1 or
ASCII, so it's converted into "&#9731" for those encodings::

 print(tag.encode("utf-8"))
 # <b>☃</b>

 print tag.encode("latin-1")
 # <b>&#9731;</b>

 print tag.encode("ascii")
 # <b>&#9731;</b>

Unicode, Dammit
---------------

You can use Unicode, Dammit without using Beautiful Soup. It's useful
whenever you have data in an unknown encoding and you just want it to
become Unicode::

 from bs4 import UnicodeDammit
 dammit = UnicodeDammit("Sacr\xc3\xa9 bleu!")
 print(dammit.unicode_markup)
 # Sacré bleu!
 dammit.original_encoding
 # 'utf-8'

Unicode, Dammit's guesses will get a lot more accurate if you install
the ``chardet`` or ``cchardet`` Python libraries. The more data you
give Unicode, Dammit, the more accurately it will guess. If you have
your own suspicions as to what the encoding might be, you can pass
them in as a list::

 dammit = UnicodeDammit("Sacr\xe9 bleu!", ["latin-1", "iso-8859-1"])
 print(dammit.unicode_markup)
 # Sacré bleu!
 dammit.original_encoding
 # 'latin-1'

Unicode, Dammit has two special features that Beautiful Soup doesn't
use.

Smart quotes
^^^^^^^^^^^^

You can use Unicode, Dammit to convert Microsoft smart quotes to HTML or XML
entities::

 markup = b"<p>I just \x93love\x94 Microsoft Word\x92s smart quotes</p>"

 UnicodeDammit(markup, ["windows-1252"], smart_quotes_to="html").unicode_markup
 # u'<p>I just &ldquo;love&rdquo; Microsoft Word&rsquo;s smart quotes</p>'

 UnicodeDammit(markup, ["windows-1252"], smart_quotes_to="xml").unicode_markup
 # u'<p>I just &#x201C;love&#x201D; Microsoft Word&#x2019;s smart quotes</p>'

You can also convert Microsoft smart quotes to ASCII quotes::

 UnicodeDammit(markup, ["windows-1252"], smart_quotes_to="ascii").unicode_markup
 # u'<p>I just "love" Microsoft Word\'s smart quotes</p>'

Hopefully you'll find this feature useful, but Beautiful Soup doesn't
use it. Beautiful Soup prefers the default behavior, which is to
convert Microsoft smart quotes to Unicode characters along with
everything else::

 UnicodeDammit(markup, ["windows-1252"]).unicode_markup
 # u'<p>I just \u201clove\u201d Microsoft Word\u2019s smart quotes</p>'

Inconsistent encodings
^^^^^^^^^^^^^^^^^^^^^^

Sometimes a document is mostly in UTF-8, but contains Windows-1252
characters such as (again) Microsoft smart quotes. This can happen
when a website includes data from multiple sources. You can use
``UnicodeDammit.detwingle()`` to turn such a document into pure
UTF-8. Here's a simple example::

 snowmen = (u"\N{SNOWMAN}" * 3)
 quote = (u"\N{LEFT DOUBLE QUOTATION MARK}I like snowmen!\N{RIGHT DOUBLE QUOTATION MARK}")
 doc = snowmen.encode("utf8") + quote.encode("windows_1252")

This document is a mess. The snowmen are in UTF-8 and the quotes are
in Windows-1252. You can display the snowmen or the quotes, but not
both::

 print(doc)
 # ☃☃☃�I like snowmen!�

 print(doc.decode("windows-1252"))
 # â˜ƒâ˜ƒâ˜ƒ“I like snowmen!”

Decoding the document as UTF-8 raises a ``UnicodeDecodeError``, and
decoding it as Windows-1252 gives you gibberish. Fortunately,
``UnicodeDammit.detwingle()`` will convert the string to pure UTF-8,
allowing you to decode it to Unicode and display the snowmen and quote
marks simultaneously::

 new_doc = UnicodeDammit.detwingle(doc)
 print(new_doc.decode("utf8"))
 # ☃☃☃“I like snowmen!”

``UnicodeDammit.detwingle()`` only knows how to handle Windows-1252
embedded in UTF-8 (or vice versa, I suppose), but this is the most
common case.

Note that you must know to call ``UnicodeDammit.detwingle()`` on your
data before passing it into ``BeautifulSoup`` or the ``UnicodeDammit``
constructor. Beautiful Soup assumes that a document has a single
encoding, whatever it might be. If you pass it a document that
contains both UTF-8 and Windows-1252, it's likely to think the whole
document is Windows-1252, and the document will come out looking like
``â˜ƒâ˜ƒâ˜ƒ“I like snowmen!”``.

``UnicodeDammit.detwingle()`` is new in Beautiful Soup 4.1.0.


Comparing objects for equality
==============================

Beautiful Soup says that two ``NavigableString`` or ``Tag`` objects
are equal when they represent the same HTML or XML markup. In this
example, the two <b> tags are treated as equal, even though they live
in different parts of the object tree, because they both look like
"<b>pizza</b>"::

 markup = "<p>I want <b>pizza</b> and more <b>pizza</b>!</p>"
 soup = BeautifulSoup(markup, 'html.parser')
 first_b, second_b = soup.find_all('b')
 print first_b == second_b
 # True

 print first_b.previous_element == second_b.previous_element
 # False

If you want to see whether two variables refer to exactly the same
object, use `is`::

 print first_b is second_b
 # False

Copying Beautiful Soup objects
==============================

You can use ``copy.copy()`` to create a copy of any ``Tag`` or
``NavigableString``::

 import copy
 p_copy = copy.copy(soup.p)
 print p_copy
 # <p>I want <b>pizza</b> and more <b>pizza</b>!</p>

The copy is considered equal to the original, since it represents the
same markup as the original, but it's not the same object::

 print soup.p == p_copy
 # True

 print soup.p is p_copy
 # False

The only real difference is that the copy is completely detached from
the original Beautiful Soup object tree, just as if ``extract()`` had
been called on it::

 print p_copy.parent
 # None

This is because two different ``Tag`` objects can't occupy the same
space at the same time.


Analisando apenas parte de um documento
=======================================

Digamos que você queira usar o Beautiful Soup para ver as tags <a> do
documento. É um desperdício de tempo e memória analisar o documento
inteiro e, em seguida, passar por ele procurando pelas tags <a>. Seria
muito mais rápido ignorar o que não era uma tag <a> desde o início. A
classe ``SoupStrainer`` te permite escolher que partes do documento de
entrada que serão analisadas. Você só precisa criar um
``SoupStrainer``e o passa-lo pelo construtor ``BeautifulSoup`` como o
argumento ``parse_only`` (analisar somente).

(Observe que *este recurso não funcionará se você estiver usando o
analisado html5lib*. Se você usar o html5lib, o documento inteiro será
analisado independentemete. Isso ocorre porque o html5lib reorganiza
constantemente a árvore de análise conforme trabalha e se alguma parte
do documento não encaixar na árvore de análise, a análise irá falhar.
Para evitar confusão, nos exemplos abaixo eu vou forçar o Beautiful
Soup a usar o analisador built-in do Python.)


``SoupStrainer``
----------------

A classe ``SoupStrainer`` usa os mesmos argumentos de um método típico
de `Busca em Árvore`_: :ref:`name <name>`, :ref:`attrs <attrs>`,
:ref:`string <string>`, e :ref:`**kwargs <kwargs>`. Abaixo três
 objetos ``SoupStrainer``::

 from bs4 import SoupStrainer

 only_a_tags = SoupStrainer("a")

 only_tags_with_id_link2 = SoupStrainer(id="link2")

 def is_short_string(string):
     return len(string) < 10

 only_short_strings = SoupStrainer(string=is_short_string)

Eu vou retomar o documento "three sisters" mais uma vez, e nós veremos
como documento  fica ao ser analisado com estes três objetos
``SoupStrainer``:

 html_doc = """
 <html><head><title>The Dormouse's story</title></head>
 <body>
 <p class="title"><b>The Dormouse's story</b></p>

 <p class="story">Once upon a time there were three little sisters; and their names were
 <a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
 <a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
 <a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
 and they lived at the bottom of a well.</p>

 <p class="story">...</p>
 """

 print(BeautifulSoup(html_doc, "html.parser", parse_only=only_a_tags).prettify())
 # <a class="sister" href="http://example.com/elsie" id="link1">
 #  Elsie
 # </a>
 # <a class="sister" href="http://example.com/lacie" id="link2">
 #  Lacie
 # </a>
 # <a class="sister" href="http://example.com/tillie" id="link3">
 #  Tillie
 # </a>

 print(BeautifulSoup(html_doc, "html.parser", parse_only=only_tags_with_id_link2).prettify())
 # <a class="sister" href="http://example.com/lacie" id="link2">
 #  Lacie
 # </a>

 print(BeautifulSoup(html_doc, "html.parser", parse_only=only_short_strings).prettify())
 # Elsie
 # ,
 # Lacie
 # and
 # Tillie
 # ...
 #

Você também pode passar um Objeto ``SoupStrainer`` como argumento de
qualquer um dos métodos mencionados na seção `Busca em Àrvore`_.
Isso provavelmente não é muito útil, mas eu pensei em mencioná-lo::

 soup = BeautifulSoup(html_doc)
 soup.find_all(only_short_strings)
 # [u'\n\n', u'\n\n', u'Elsie', u',\n', u'Lacie', u' and\n', u'Tillie',
 #  u'\n\n', u'...', u'\n']

Troubleshooting
===============

.. _diagnose:

``diagnose()``
--------------

If you're having trouble understanding what Beautiful Soup does to a
document, pass the document into the ``diagnose()`` function. (New in
Beautiful Soup 4.2.0.)  Beautiful Soup will print out a report showing
you how different parsers handle the document, and tell you if you're
missing a parser that Beautiful Soup could be using::

 from bs4.diagnose import diagnose
 with open("bad.html") as fp:
     data = fp.read()
 diagnose(data)

 # Diagnostic running on Beautiful Soup 4.2.0
 # Python version 2.7.3 (default, Aug  1 2012, 05:16:07)
 # I noticed that html5lib is not installed. Installing it may help.
 # Found lxml version 2.3.2.0
 #
 # Trying to parse your data with html.parser
 # Here's what html.parser did with the document:
 # ...

Just looking at the output of diagnose() may show you how to solve the
problem. Even if not, you can paste the output of ``diagnose()`` when
asking for help.

Errors when parsing a document
------------------------------

There are two different kinds of parse errors. There are crashes,
where you feed a document to Beautiful Soup and it raises an
exception, usually an ``HTMLParser.HTMLParseError``. And there is
unexpected behavior, where a Beautiful Soup parse tree looks a lot
different than the document used to create it.

Almost none of these problems turn out to be problems with Beautiful
Soup. This is not because Beautiful Soup is an amazingly well-written
piece of software. It's because Beautiful Soup doesn't include any
parsing code. Instead, it relies on external parsers. If one parser
isn't working on a certain document, the best solution is to try a
different parser. See `Installing a parser`_ for details and a parser
comparison.

The most common parse errors are ``HTMLParser.HTMLParseError:
malformed start tag`` and ``HTMLParser.HTMLParseError: bad end
tag``. These are both generated by Python's built-in HTML parser
library, and the solution is to :ref:`install lxml or
html5lib. <parser-installation>`

The most common type of unexpected behavior is that you can't find a
tag that you know is in the document. You saw it going in, but
``find_all()`` returns ``[]`` or ``find()`` returns ``None``. This is
another common problem with Python's built-in HTML parser, which
sometimes skips tags it doesn't understand.  Again, the solution is to
:ref:`install lxml or html5lib. <parser-installation>`

Version mismatch problems
-------------------------

* ``SyntaxError: Invalid syntax`` (on the line ``ROOT_TAG_NAME =
  u'[document]'``): Caused by running the Python 2 version of
  Beautiful Soup under Python 3, without converting the code.

* ``ImportError: No module named HTMLParser`` - Caused by running the
  Python 2 version of Beautiful Soup under Python 3.

* ``ImportError: No module named html.parser`` - Caused by running the
  Python 3 version of Beautiful Soup under Python 2.

* ``ImportError: No module named BeautifulSoup`` - Caused by running
  Beautiful Soup 3 code on a system that doesn't have BS3
  installed. Or, by writing Beautiful Soup 4 code without knowing that
  the package name has changed to ``bs4``.

* ``ImportError: No module named bs4`` - Caused by running Beautiful
  Soup 4 code on a system that doesn't have BS4 installed.

.. _parsing-xml:

Parsing XML
-----------

By default, Beautiful Soup parses documents as HTML. To parse a
document as XML, pass in "xml" as the second argument to the
``BeautifulSoup`` constructor::

 soup = BeautifulSoup(markup, "xml")

You'll need to :ref:`have lxml installed <parser-installation>`.

Outros problemas de analisadores
---------------------

* Se seu script funciona em um computador mas não em outro, ou em um ambiente virtual mas não em outro, ou fora do ambiente virtual mas não dentro, é provável que a causa seja que os dois ambientes possuam diferentes bibliotecas analisadoras. Por exemplo, você pode ter desenvolvido o script em um computador que possuía lxml instalado, e então tentado executá-lo em um computador que possuía apenas o html5lib instalado. Veja `Diferenças entre analisadores <#differences-between-parsers>`_ para saber o porquê da importância disso, e corrija o problema mencionando uma biblioteca analisadora específica no construtor do ``BeautifulSoup``.

* Devido as `tags e atributos HTML não serem sensíveis a letra maiúsculas e minúsculas <http://www.w3.org/TR/html5/syntax.html#syntax>`_, todos os três analisadores HTML convertem os nomes de tags e atributos para letras minúsculas. Ou seja, a  marcação `<TAG></TAG>` é convertida para `<tag></tag>`. Caso você queira preservar tags e atributos escritos em maiúsculo ou de forma mista, você precisará `analisar o documento como XML <#parsing-xml>`_.

.. _misc:

Miscelânea
-------------
  
* ``Codec UnicodeEncodeError: 'charmap' codec can't encode character
  u'\xfoo' in position bar`` (ou apenas sobre qualquer outra
  ``UnicodeEncodeError``) - Isto não é um problema com o Beautiful Soup.
  Esse problema aparece em duas situações principais. Primeiro, quando você tenta
  imprimir um caractere Unicode que seu console não sabe como
  exibir. (Veja `esta página no wiki do Python
  <http://wiki.python.org/moin/PrintFails>`_ para ajuda.) Segundo, quando
  você está escrevendo em um arquivo e você passa um caractere Unicode
  não suportado pela sua codificação padrão. Neste caso, a mais simples
  solução é codificar explicitamente a string Unicode em UTF-8 com
  ``u.encode ("utf8")``.

* ``KeyError: [attr]``- Causado por acessar ``tag ['attr']`` quando a
  tag em questão não define o atributo ``attr``. Os erros
  mais comuns são ``KeyError: 'href'`` e ``KeyError:
  'class'``. Use ``tag.get ('attr')`` se você não tiver certeza que ``attr`` está
  definido, assim como você faria com um dicionário Python.

* ``AttributeError: 'ResultSet' object has no attribute 'foo'`` - Isto
  geralmente acontece porque você espera que ``find_all ()`` retorne uma
  tag ou string única. Mas ``find_all ()`` retorna uma lista de tags
  e strings - um objeto ``ResultSet``. Você precisa iterar sobre a
  lista e olhar o ``.foo`` de cada um. Ou, se você realmente só
  quer um resultado, você precisa usar ``find ()`` em vez de
  ``find_all ()``.

* ``AttributeError: 'NoneType' object has no attribute 'foo'`` -
  geralmente acontece porque você chamou ``find ()`` e então tentou
  acessar o atributo `.foo`` do resultado. Mas no seu caso,
  ``find ()`` não encontrou nada, então retornou ``None``, em vez de
  retornar uma tag ou uma string. Você precisa descobrir porquê sua
  chamada ``find ()`` não está retornando nada.



Melhorando a Performance
------------------------

O Beautiful Soup nunca será tão rápido quanto aos analisadores sintáticos que ele gerencia.
Se o tempo de resposta for algo crítico, se você está pagando por hora por recursos
computacionais ou se há alguma outra razão pela qual o tempo computacional é mais
valioso que o tempo do programador, então você deveria esquecer do Beautiful Soup
e trabalhar diretamente com `lxml <http://lxml.de/>`_.

Dito isso, existem maneiras de melhorar a velocidade do Beautiful Soup. Se você ainda não
começou a usar o lxml como um analisador implícito, meu conselho é `começar <parser-installation>`_.
O Beautiful Soup analisa documentos de uma forma significativamente mais rápida usando
lxml do que usando um html.parser ou html5lib.

Você pode acelerar a detecção de codificação de uma maneira significativa instalando
a biblioteca `cchardet <http://pypi.python.org/pypi/cchardet/>`_.

`Analisar apenas parte de um documento`_ não irá lhe poupar muito tempo ao analisar o
documento, mas pode lhe economizar muita memória e tornar a `buscas` no documento muito mais rápidas.


Beautiful Soup 3
================

Beautiful Soup 3 is the previous release series, and is no longer
being actively developed. It's currently packaged with all major Linux
distributions:

:kbd:`$ apt-get install python-beautifulsoup`

It's also published through PyPi as ``BeautifulSoup``.:

:kbd:`$ easy_install BeautifulSoup`

:kbd:`$ pip install BeautifulSoup`

You can also `download a tarball of Beautiful Soup 3.2.0
<http://www.crummy.com/software/BeautifulSoup/bs3/download/3.x/BeautifulSoup-3.2.0.tar.gz>`_.

If you ran ``easy_install beautifulsoup`` or ``easy_install
BeautifulSoup``, but your code doesn't work, you installed Beautiful
Soup 3 by mistake. You need to run ``easy_install beautifulsoup4``.

`The documentation for Beautiful Soup 3 is archived online
<http://www.crummy.com/software/BeautifulSoup/bs3/documentation.html>`_.

Portando código para BS4
------------------------

A maior parte do código escrito com Beautiful Soup 3 funcionará também com a 
Beautiful Soup 4 com uma simples mudança. Tudo que você precisa fazer é mudar o
nome do pacote de ``BeautifulSoup`` para ``bs4``. Então, isso::

  from BeautifulSoup import BeautifulSoup

fica assim::

  from bs4 import BeautifulSoup

* Se você tiver um ``ImportError`` "No module named BeautifulSoup", seu
  problema é que está tentando rodar código da Beautiful Soup 3, porém,
  há somente a Beautiful Soup 4 instalada.

* Se você tiver um ``ImportError`` "No module named bs4", seu
  problema é que está tentando rodar código da Beautiful Soup 4, porém,
  há somente a Beautiful Soup 3 instalada.

Embora o BS4 seja na maior parte compatível com o BS3, a maioria de seus
métodos foram descontinuados e receberam novos nomes de acordo com a `PEP 8
<http://www.python.org/dev/peps/pep-0008/>`_. Existem inúmeras outras
renomeações e mudanças, e algumas delas quebram a retrocompatibilidade.

Aqui está o que você precisa saber para converter seu código e hábitos BS3 para BS4:

Você precisa de um parser
^^^^^^^^^^^^^^^^^^^^^^^^^

A Beautiful Soup 3 utilizava o ``SGMLParser`` do Python, um módulo que foi
depreciado e removido no Python 3.0. Beautiful Soup 4 usa
``html.parser`` por padrão, mas você pode usar lxml ou html5lib 
ao invés. Veja `Instalando um parser`_ para comparação.

Já que ``html.parser`` não é o mesmo ``SGMLParser``, podemos ver 
que a Beautiful Soup 4 lhe dá uma árvore de parse diferente da 
Beautiful Soup 3 para a mesma marcação. Se você trocar o ``html.parser``
por lxml ou html5lib, pode-se ver que a árvore de parse muda
novamente. Se isso acontecer, você precisará atualizar seu código de scraping para
lidar com a nova árvore.

Nomes dos métodos
^^^^^^^^^^^^^^^^^

* ``renderContents`` -> ``encode_contents``
* ``replaceWith`` -> ``replace_with``
* ``replaceWithChildren`` -> ``unwrap``
* ``findAll`` -> ``find_all``
* ``findAllNext`` -> ``find_all_next``
* ``findAllPrevious`` -> ``find_all_previous``
* ``findNext`` -> ``find_next``
* ``findNextSibling`` -> ``find_next_sibling``
* ``findNextSiblings`` -> ``find_next_siblings``
* ``findParent`` -> ``find_parent``
* ``findParents`` -> ``find_parents``
* ``findPrevious`` -> ``find_previous``
* ``findPreviousSibling`` -> ``find_previous_sibling``
* ``findPreviousSiblings`` -> ``find_previous_siblings``
* ``nextSibling`` -> ``next_sibling``
* ``previousSibling`` -> ``previous_sibling``

Alguns argumentos do construtor Beautiful Soup foram renomeados pelos
mesmos motivos:

* ``BeautifulSoup(parseOnlyThese=...)`` -> ``BeautifulSoup(parse_only=...)``
* ``BeautifulSoup(fromEncoding=...)`` -> ``BeautifulSoup(from_encoding=...)``

Renomeei um método para compatibilidade com o Python 3:

* ``Tag.has_key()`` -> ``Tag.has_attr()``

Renomeei um atributo para usar uma terminologia mais precisa:

* ``Tag.isSelfClosing`` -> ``Tag.is_empty_element``

Renomeei três atributos para evitar o uso de palavras reservadas
no Python. Ao contrário das outras, essas mudanças não são *retrocompatíveis.* 
Se você utilizou um desses atributos no BS3, seu código irá quebrar
no BS4 até você alterá-los.

* ``UnicodeDammit.unicode`` -> ``UnicodeDammit.unicode_markup``
* ``Tag.next`` -> ``Tag.next_element``
* ``Tag.previous`` -> ``Tag.previous_element``

Geradores
^^^^^^^^^

Eu dei aos geradores nomes compatíveis com a PEP 8 e os transformei em
propriedades:

* ``childGenerator()`` -> ``children``
* ``nextGenerator()`` -> ``next_elements``
* ``nextSiblingGenerator()`` -> ``next_siblings``
* ``previousGenerator()`` -> ``previous_elements``
* ``previousSiblingGenerator()`` -> ``previous_siblings``
* ``recursiveChildGenerator()`` -> ``descendants``
* ``parentGenerator()`` -> ``parents``

Então ao invés disso::

 for parent in tag.parentGenerator():
     ...

Você pode escrever isso::

 for parent in tag.parents:
     ...

(Mas o código antigo ainda funciona.)

Alguns dos geradores costumavam retornar ``None`` ao serem executados, e
então paravam. Isso era um bug. Agora os geradores simplesmente param.

Existem dois novos geradores, :ref:`.strings and
.stripped_strings <string-generators>`. ``.strings`` retorna
objetos NavigableString e ``stripped_strings`` retornam strings
Python que tiveram espaços em branco retirados.

XML
^^^

Não existe mais a classe ``BeautifulStoneSoup`` para parsear XML. Para
isso, é necessário que você passe "xml" como o segundo argumento do
construtor ``BeautifulSoup``. Pela mesma razão, o construtor 
``BeautifulSoup`` não reconhece mais o argumento ``isHTML``.

O tratamento da Beautiful Soup em tags de elementos XML vazios foi
melhorado. Anteriormente, quando você parseava XML, tinha que dizer explicitamente
quais tags eram consideradas tags de elementos vazios. O argumento ``selfClosingTags``
não é mais reconhecido. Ao invés disso, a Beautiful Soup considera 
que qualquer tag vazia é uma tag de elemento vazio. E se
você adiciona um elemento filho a uma tag de elemento vazio, ele deixa de ser um
tag de elemento vazio.

Entidades
^^^^^^^^^

Uma entidade HTML ou XML, em sua entrada, é sempre convertida no
caractere unicode correspondente a ela. Beautiful Soup 3 teve um número de
formas de lidar com entidades sobrepostas, que foram
removidas. O construtor ``BeautifulSoup`` não reconhece mais os argumentos
``smartQuotesTo`` ou ``convertEntities``. (`Unicode,
Dammit`_ ainda tem ``smart_quotes_to``, mas seu padrão agora é transformar
smart quotes em Unicode.) As constantes ``HTML_ENTITIES``,
``XML_ENTITIES`` e ``XHTML_ENTITIES`` foram removidos, pois
configuram um recurso (transformar algumas entidades, mas não todas em
caracteres unicode) que não existe mais.

Se você quiser transformar caracteres Unicode novamente em entidades HTML
na saída, ao invés de transformá-los em caracteres UTF-8, você precisa
utilizar um formatador de saída :ref:`<formatadores_de_saida>`.

Outras Informações
^^^^^^^^^^^^^^^^^^

:ref:`Tag.string <.string>` agora opera recursivamente. Se a tag A
contém uma única tag B e nada mais, então A.string é o mesmo que
B.string. (Anteriormente, era None.)

`Atributos de valor múltiplo`_ como ``class`` possuem listas de strings como
seus valores, não strings. Isso pode afetar a maneira como você pesquisa por classes 
CSS.

Se você passar um dos métodos ``find*``, ambos :ref:`string <string>` e
um atributo de tag como :ref:`name <name>`, Beautiful Soup vai
buscar tags que correspondam aos seus critérios específicos e
:ref:`Tag.string <.string>` correspondam ao seu valor para :ref:`string
<string> `. Não "encontrará" as strings em si. Anteriormente,
A Beautiful Soup ignorava os atributos da tag e procurava
por strings.

O construtor ``BeautifulSoup`` não reconhece mais o
argumento `markupMassage`. Agora é responsabilidade do parser
manipular a marcação corretamente.

As classes de parsers alternativos raramente usadas, como
``ICantBelieveItsBeautifulSoup`` e ``BeautifulSOAP`` foram
removidas. Agora a decisão é do parser de como lidar com marcação 
ambígua.

O método ``prettify()`` agora retorna uma string Unicode, e não mais uma bytestring.
