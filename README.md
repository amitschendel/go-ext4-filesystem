# go-ext4-filesystem 

go-ext4-filesystem is ext4 filesystem stream parser.  
This library was created by @masahiro331 and implement io/fs interface.
I changed it to not support io/fs so It can be more flexable with the usage.


## Quick Start
```go
func Walk(ext4fs *ext4.FileSystem, root string) error {
	entries, err := ext4fs.ReadDir(root)
	if err != nil {
		return err
	}

	for _, entry := range entries {
		fmt.Printf("Path: %s\n", path.Join(root, entry.Name()))

		info, err := ext4fs.Stat(path.Join(root, entry.Name()))
		if err != nil {
			return err
		}

		fmt.Printf("Creation Time: %v\n", info.CreationTime())

		if entry.IsDir() {
			if err := Walk(ext4fs, path.Join(root, entry.Name())); err != nil {
				return err
			}
		}
	}

	return nil
}

func main() {
	f, err := os.Open("ext4_image.img")
	if err != nil {
		log.Fatal(err)
	}
	info, _ := f.Stat()
	filesystem, err := ext4.NewFS(*io.NewSectionReader(f, 0, info.Size()), nil)
	if err != nil {
		log.Fatal(err)
	}

	Walk(filesystem, "/")
}
```
