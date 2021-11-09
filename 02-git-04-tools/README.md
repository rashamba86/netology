
1. Используя `git show aefea` получаю всю информацию о коммите. Полный хеш - `aefead2207ef7e2aa5dc81a34aedf0cad4c32545`, комментарий - `Update CHANGELOG.md`.

2. Используя `git show 85024d3` получаю информацию о теге - `tag: v0.12.23`.

3. Использую `git log --graph b8d720` и получаю информацию о том, что это merge-коммит, родители которого коммиты `9ea88f22fc6269854151c571162c5bcf958bee2b` и `56cd7859e05c36c06b56d013b55a252d0bb7e158`. У merge-коммита два предка, поэтому посмотреть их можно командой `git show b8d720^1` и `git show b8d720^2` для первого и второго предка соответственно.

4. С помощью `git log v0.12.23..v0.12.24` можно посмотреть список коммитов, которые есть в теге v0.12.24, но нет в теге v0.12.23.
    * commit 225466bc3e5f35baa5d07197bbc079345b77525e  
        Cleanup after v0.12.23 release
    * commit dd01a35078f040ca984cdd349f18d0b67e486c35  
        Update CHANGELOG.md
    * commit 4b6d06cc5dcb78af637bbb19c198faff37a066ed  
        Update CHANGELOG.md
    * commit d5f9411f5108260320064349b757f55c09bc4b80  
        command: Fix bug when using terraform login on Windows
    * commit 06275647e2b53d97d4f0a19a0fec11f6d69820b5  
        Update CHANGELOG.md
    * commit 5c619ca1baf2e21a155fcdb4c264cc9e24a2a353  
        website: Remove links to the getting started guide`s old location  
        Since these links were in the soon-to-be-deprecated 0.11 language section, I think we can just remove them without needing to find an equivalent link
    * commit 6ae64e247b332925b872447e9ce869657281c2bf  
        registry: Fix panic when server is unreachable  
        Fixes #24384
    * commit 3f235065b9347a758efadc92295b540ee0a5e26e  
        Update CHANGELOG.md
    * commit b14b74c4939dcab573326f4e3ee2a62e23e12f89  
        [Website] vmc provider links

5. Вводим команду ``git log -S`func providerSource` --oneline``. В ответ получаем два коммита, в которых добавляется или удаляется искомая функция: `5af1e6234`, `8c928e835`. Просматривая найденные коммиты с помощью `git show` находим информацию о добавлении искомой функции в коммите `8c928e83589d90a031f811fae52a81be7153e82f`.

6. Командой `git grep -c globalPluginDirs` определяем файлы в которых упоминается искомая функция `globalPluginDirs`.
    * commands.go:2
    * internal/command/cliconfig/config_unix.go:1
    * plugins.go:2
    
   Далее, используя команду `git log - L :globalPluginDirs:имя файла` поочередно проверяем все файлы, в которых происходило изменение искомой функции. В результате проверки находим следующие коммиты:
    * commit 78b12205587fe839f10d946ea3fdc06719decb05
    * commit 52dbf94834cb970b510f2fba853a5b49ad9b1a46
    * commit 41ab0aef7a0fe030e84018973a64135b11abcd70
    * commit 66ebff90cdfaa6938f26f908c7ebad8d547fea17
    * commit 8364383c359a6b738a436d1b7745ccdce178df47
    
   Все коммиты находятся в файле plugins.go

7. С помощью команды `git log -SsynchronizedWriter` определяю инициирующий коммит, где появляется функция `synchronizedWriter`: `commit 5ac311e2a91e381e2f52234668b49ba670aa0fe5`. Просматриваю коммит `git show 5ac311e2a91e381e2f52234668b49ba670aa0fe5` и нахожу автора - `Author: Martin Atkins <mart@degeneration.co.uk>`.
