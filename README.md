Using Conform + Distillery in an umbrella project causes some problems when you try to start the release.

Here is how you can reproduce it:

1. Compiling a release:
```
$ MIX_ENV=prod mix release --env=prod
WARN:  Missing plugins: [rebar3_hex]
==> neotoma (compile)
==> conform
Compiling 1 file (.erl)
Compiling 19 files (.ex)
warning: function Mix.Releases.Utils.insecure_mkdir_temp/0 is undefined (module Mix.Releases.Utils is not available)
Found at 2 locations:
  lib/conform/releases/plugin.ex:131
  lib/conform/releases/plugin.ex:157

Generated conform app
==> distillery
Compiling 19 files (.ex)
Generated distillery app
==> app1
Compiling 2 files (.ex)
Generated app1 app
==> app2
Compiling 2 files (.ex)
Generated app2 app
==> Assembling release..
==> Building release test_conform_umbrella:0.1.0 using environment prod
==> Including ERTS 9.0 from /usr/local/Cellar/erlang/20.0/lib/erlang/erts-9.0
==> Packaging release..
==> Release successfully built!
    You can run it in one of the following ways:
      Interactive: _build/prod/rel/test_conform_umbrella/bin/test_conform_umbrella console
      Foreground: _build/prod/rel/test_conform_umbrella/bin/test_conform_umbrella foreground
      Daemon: _build/prod/rel/test_conform_umbrella/bin/test_conform_umbrella start
```

2. Run it:
```
$ _build/prod/rel/test_conform_umbrella/bin/test_conform_umbrella console
** (FunctionClauseError) no function clause matching in Conform.Schema.Validator.from_quoted/1
    (conform) lib/conform/schema/validator.ex:48: Conform.Schema.Validator.from_quoted(Conform.Validators.RangeValidator)
    (elixir) lib/enum.ex:1229: Enum."-map/2-lists^map/1-0-"/2
    (conform) lib/conform/schema.ex:191: Conform.Schema.from/2
    (conform) lib/conform.ex:93: Conform.process/1
    (elixir) lib/kernel/cli.ex:76: anonymous fn/3 in Kernel.CLI.exec_fun/2
```

However there is a odd work-around for this issue - if you compile the release twice, then it will work correct.

1. Compile it twice:
```
$ MIX_ENV=prod mix release --env=prod && MIX_ENV=prod mix release --env=prod
WARN:  Missing plugins: [rebar3_hex]
==> neotoma (compile)
==> conform
Compiling 1 file (.erl)
Compiling 19 files (.ex)
warning: function Mix.Releases.Utils.insecure_mkdir_temp/0 is undefined (module Mix.Releases.Utils is not available)
Found at 2 locations:
  lib/conform/releases/plugin.ex:131
  lib/conform/releases/plugin.ex:157

Generated conform app
==> distillery
Compiling 19 files (.ex)
Generated distillery app
==> app1
Compiling 2 files (.ex)
Generated app1 app
==> app2
Compiling 2 files (.ex)
Generated app2 app
==> Assembling release..
==> Building release test_conform_umbrella:0.1.0 using environment prod
==> Including ERTS 9.0 from /usr/local/Cellar/erlang/20.0/lib/erlang/erts-9.0
==> Packaging release..
==> Release successfully built!
    You can run it in one of the following ways:
      Interactive: _build/prod/rel/test_conform_umbrella/bin/test_conform_umbrella console
      Foreground: _build/prod/rel/test_conform_umbrella/bin/test_conform_umbrella foreground
      Daemon: _build/prod/rel/test_conform_umbrella/bin/test_conform_umbrella start
==> Assembling release..
==> Building release test_conform_umbrella:0.1.0 using environment prod
==> Including ERTS 9.0 from /usr/local/Cellar/erlang/20.0/lib/erlang/erts-9.0
==> Packaging release..
==> Release successfully built!
    You can run it in one of the following ways:
      Interactive: _build/prod/rel/test_conform_umbrella/bin/test_conform_umbrella console
      Foreground: _build/prod/rel/test_conform_umbrella/bin/test_conform_umbrella foreground
      Daemon: _build/prod/rel/test_conform_umbrella/bin/test_conform_umbrella start
```

2. Run it:
```
$ _build/prod/rel/test_conform_umbrella/bin/test_conform_umbrella console
Generated sys.config in /private/tmp/test_conform_umbrella/_build/prod/rel/test_conform_umbrella/var
Erlang/OTP 20 [erts-9.0] [source] [64-bit] [smp:8:8] [ds:8:8:10] [async-threads:10] [hipe] [kernel-poll:false] [dtrace]

Hello App1!
Hello App2!
Interactive Elixir (1.4.5) - press Ctrl+C to exit (type h() ENTER for help)
iex(test_conform_umbrella@127.0.0.1)1>
```
