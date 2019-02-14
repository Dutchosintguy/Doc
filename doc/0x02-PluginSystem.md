# Plugin System

GOCA&copy; plugins are code snippets developed independetly from the GOCA&copy; core. Even so, plugins have to be linked to GOCA&copy; and have some kind of `dorker` or `crawler` associated.

The plugin system is a common way to import code from outside of the main package or program. This system is based in the way that Go import packages. This system allows plugin developers to call a register method in `init` function of each file/package.

## Registering a plugin

A plugin is a Golang package, just that. The package must import and call the `goca.RegisterPlugin` to register itself as plugin. For each plugin, a unique plugin name must be especified, it also needs a type, an associeted list of MIME type, an _EntryPoint_, and a matcher function.

```go
goca.RegisterPlugin("audio", goca.Plugin{
    Type:   "mp3",
    Assoc:  []string{"audio/mpeg"},
    Action: setup,
    Matcher: matcherFunc,
})
```

In this particular case, a plugin named `audio` is being definied. This plugin is defined using `goca.Plugin`.

Inside this structure, the type of plugin is defined (`mp3` in this case). The type will be used to call or initialize the plugin from the command line. This means you can have serveral plugins (with different names) associated to the same plugin type. Moreover, a MIMEType list is defined which are associated to this plugin. It is possible that a plugin can process more than one kind of MIMEType. The plugin entry point is especified in the `Action` field.

Finally, in case you develop a plugin for a wierd file type, you can define a file type matcher function. That function indicates to GOCA&copy; when a plugin must be fired and analyze its content.

***

## Communication Plugin - GOCA&copy;

### Global Object

Communication GOCA&copy; - Plugins works using events. Each plugin is receiving a controller where it can subscribe and publish events.

It is recommended to use a global object inside the plugins to keep the plugin alive and give the possibility to subscribe or publish events when necessary.

An example for mp3 plugin.

```go
var mp3 *mp3MetaExtractor

type mp3MetaExtractor struct {
    goca.Manager
}
```

### Entry point

Function defined as entry point should initialize the global object and assign the `goca.Manager` controller received as parameter. The entrypoint should satisfied `func(goca.Manager) error`.

From this point, the plugin will be subscribed to the events.

```go
func setup(m goca.Manager) error {
    mp3 = new(mp3MetaExtractor)
    mp3.Manager = m
    mp3.Subscribe(goca.Topics["NewURL"], mp3.readMP3)
    return nil
}
```

As you can imagine `mp3.readMP3` is where the magic happends.

### Emitting `goca.Output`

When plugins' work is done, it has to publish an event with the result. For that matter, it is necessary to emit or publish an event with the `goca.Output` object.

```go
func (mp3 *mp3MetaExtractor) readMP3(url string, data []byte) {
    log.Debugf("[MP3] Received Data Length: %d - URL: %s\n", len(data), url)
    buf := bytes.NewReader(data)
    m, err := tag.ReadFrom(buf)
    if err == nil {
        out := goca.NewOutput()
        out.Title = m.Title()
        out.Album = m.Album()
        out.Artist = m.Artist()
        out.AlbumArtist = m.AlbumArtist()
        out.Comment = m.Comment()
        out.Composer = m.Composer()
        out.Disc.A, out.Disc.B = m.Disc()
        out.Genre = m.Genre()
        out.Lyrics = m.Lyrics()
        out.Track.A, out.Track.B = m.Track()

        mp3.Publish(goca.Topics["NewOutput"], plugName, url, out)
    } else {
        log.Errorf("[MP3] - Err: %s\n", err.Error())
    }
}
```

## Goca integration

The integratios happens in the GOCA&copy;'s `plugins` package, inside the `plugins.go` file. In this file it is only necessary to import the new module.

```go
package plugins

import (
    _ "github.com/gocaio/goca/plugins/mp3"
)
```

If `goca.Object` does not satisfies the necessary fields to store the meta information extracted from the plugin, it is necessary to implement the required fields into `goca.Output` in a flatten way.

Finally, it is necessary to provide at least one dork for the MIMEType associated to the plugin. This is going to be in the `dorker` package, in the `dorker.go` file, where the `DorkLib` has to be updated with the required dorks.

```go
var DorkLib = dorkLib{
    "application/pdf": []Dork{
        Dork{"google", "filetype:pdf +\"%s\""},
    },
    "audio/mpeg": []Dork{
        Dork{"google", "filetype:mp3 +\"%s\""},
    },
}
```