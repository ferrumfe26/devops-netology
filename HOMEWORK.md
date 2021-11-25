Домашнее задание к занятию «2.4. Инструменты Git»

    1. Найдите полный хеш и комментарий коммита, хеш которого начинается на aefea.

         aefead2207ef7e2aa5dc81a34aedf0cad4c32545

    2. Какому тегу соответствует коммит 85024d3?

         tag: v0.12.23

    3. Сколько родителей у коммита b8d720? Напишите их хеши.

      $ git show b8d720

       commit b8d720f8340221f2146e4e4870bf2ee0bc48f2d5

       Merge: 56cd7859e 9ea88f22f


       или 

    $ git show --pretty=format:' %P' b8d720

      56cd7859e05c36c06b56d013b55a252d0bb7e158 9ea88f22fc6269854151c571162c5bcf958bee2b

    4. Перечислите хеши и комментарии всех коммитов которые были сделаны между тегами v0.12.23 и v0.12.24.

        $ git log --oneline v0.12.23..v0.12.24

        33ff1c03b (tag: v0.12.24) v0.12.24

        b14b74c49 [Website] vmc provider links

        3f235065b Update CHANGELOG.md

        6ae64e247 registry: Fix panic when server is unreachable

        5c619ca1b website: Remove links to the getting started guide's old location

        06275647e Update CHANGELOG.md

        d5f9411f5 command: Fix bug when using terraform login on Windows

        4b6d06cc5 Update CHANGELOG.md

        dd01a3507 Update CHANGELOG.md

        225466bc3 Cleanup after v0.12.23 release


    5. Найдите коммит в котором была создана функция func providerSource, ее определение в коде выглядит так func providerSource(...) (вместо троеточего перечислены аргументы).

         $ git log -S'func providerSource' --oneline

         5af1e6234 main: Honor explicit provider_installation CLI config when present

         8c928e835 main: Consult local directories as potential mirrors of providers


    6. Найдите все коммиты в которых была изменена функция globalPluginDirs.
         1. $ git grep globalPluginDirs
                commands.go:            GlobalPluginDirs: globalPluginDirs(),

                commands.go:    helperPlugins := pluginDiscovery.FindPlugins("credentials", globalPluginDirs())

                internal/command/cliconfig/config_unix.go:              // FIXME: homeDir gets called from globalPluginDirs during init, before

                plugins.go:// globalPluginDirs returns directories that should be searched for

                plugins.go:func globalPluginDirs() []string {


         2. user@Sapphire MINGW64 ~/PycharmProjects/devops-netology (main)

            $ git log -L :'func globalPluginDirs':plugins.go


    7. Кто автор функции synchronizedWriters?

          user@Sapphire MINGW64 ~/PycharmProjects/devops-netology (main)

           $ git log -S'func synchronizedWriters' --pretty=format:'%h - %an %ae'

                bdfea50cc - James Bardin j.bardin@gmail.com

                5ac311e2a - Martin Atkins mart@degeneration.co.uk


          Соответственно автор Martin Atkins