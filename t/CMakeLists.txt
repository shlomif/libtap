foreach (basename
        cmp_mem
        cmpok
        diag
        diesok
        is
        like
        simple
        skip
        synopsis
        todo
)
    add_executable ("${basename}" "${basename}.c")
    target_link_libraries ("${basename}" tap)
endforeach ()
