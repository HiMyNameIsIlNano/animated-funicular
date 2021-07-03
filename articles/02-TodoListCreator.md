I started organizing my work tasks using simple todo lists and I came up with the following two bash functions to achieve that. The script requires an environment variable called `${TODOS_OUT_FOLDER}` to be present.

```bash
todo-create() {
	if [ "$#" -gt 2 ]; then
		echo "Up to two parameter must be provided to the function."
		echo "Example: ${FUNCNAME[0]} [filename] [outdir]."
		return
	fi
	
	local file=$(date +'%Y-%m-%d').todo
	local out_dir=${TODOS_OUT_FOLDER}
	if [ "$#" -eq 2 ]; then
		file="$1".todo
		out_dir="$2"
	elif [ "$#" -eq 1 ]; then
		file="$1".todo
	else
		# If I am generating the todo file name with the default strategy, I would like them to be grouped by YYYYMM
		local YYYYMM=$(date +'%Y-%m')
		out_dir=${TODOS_OUT_FOLDER}/${YYYYMM}
	fi
	
	mkdir -p ${out_dir}
	cd ${out_dir}
	if [ ! -f "${file}" ]; then
		touch ${file}
		printf ".todo\n.done" > "${file}"
	fi
}

todo-edit() {
	if [ "$#" -gt 1 ]; then
		echo "Up to one parameter must be provided to the function."
		echo "Example: ${FUNCNAME[0]} [filename]."
		return
	fi

	if [ ! -z $1 ]; then
		todo-create "$1"
		vim ${TODOS_OUT_FOLDER}/"$1".todo	
	else
		local file_name=$(date +'%Y-%m-%d')
		local year_and_month_out_dir=$(date +'%Y-%m')
		todo-create ${file_name} ${TODOS_OUT_FOLDER}/${year_and_month_out_dir}
		vim ${TODOS_OUT_FOLDER}/${year_and_month_out_dir}/${file_name}.todo	
	fi
}
```

- usage `todo-create [filename] [outdir]`. If the filename and the outdir are not provided a file named `YYYY-MM-DD.todo` will be created under `${TODOS_OUT_FOLDER}/${YYYYMM}`
- usage `todo-edit [filename]`. If the filename is provided the file will be edited. If the file by that name does not exists, it will be created. Without any parameter the function will create a default named file with the logic described in the `todo-create` function.
