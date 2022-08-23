# Listing_File
#File
## Reqursive Full Level Deep
```shell
dir_find () {
	local dir=$1
	local indent=$2
	printf '%s%s-\n' "${##*/}-"
	for file in "$dir"/* ; do
		printf '%s%s\n' "$indent${file##*/}"
		if [[ -d $file ]] ; then
			dir_find "$file" " $indent"
		fi
	done
}
dir_find .
```

**Version with echo no formating :**
```shell
deep_dir () {
	local dir=$1
	for file in "$dir"/* ; do
		echo $file
		if [[ -d $file ]] ; then
			deep_dir "$file"
		fi
	done
}
deep_dir .
```


## Array 1 Level Deep
```shell
array_files=( $(ls) )

	for file in ${array_files[@]}
	do
		echo file
	fi
	done
```
