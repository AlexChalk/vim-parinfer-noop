## vim-parinfer-noop

It's https://github.com/bhurlow/vim-parinfer, but (by default) it does nothing.

## Why?

If you only want to modify your buffer using parinfer in precise scenarios that you explicitly define.

For example, I mostly prefer https://github.com/guns/vim-sexp, https://github.com/tpope/vim-sexp-mappings-for-regular-people, and https://github.com/kovisoft/paredit for sexp-editing, but none of these libraries will autobalance my parentheses for me if I comment out code.

By importing this library without defining any mappings or autocmds, and by leveraging https://github.com/numToStr/Comment.nvim and its `post_hook` method, I can get the behaviour I want:

```
require("Comment").setup({
  post_hook = function()
    local filetypes = { clojure = true, clojurescript = true }
    local current_filetype = vim.bo.filetype
    if filetypes[current_filetype] then
      vim.call("parinfer#process_form")
    end
  end,
})
```

tl;dr: if you want a parinfer implementation for (neo)vim, but you also want more precise control over when it modifies your buffer, then this package might be useful to you.
