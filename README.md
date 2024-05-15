## vim-parinfer-noop

It's https://github.com/bhurlow/vim-parinfer, but (by default) it does nothing.

## Use case

You may only want to run parinfer in precise scenarios that you explicitly define.

For example, I mostly prefer https://github.com/guns/vim-sexp for editing lisp, but I'd still like autobalancing to occur after using put, linewise delete, or commenting out code.

By importing this library and defining my own mappings, I can get the behaviour I want:
```lua
-- dd
vim.keymap.set("n", "dd", function()
  vim.call("parinfer#delete_line")
end, { buffer = 0, noremap = true })

-- dNk/j
vim.keymap.set("o", "j", function()
  if vim.v.operator ~= "d" then
    return "j"
  else
    return "j" .. '<Cmd>lua vim.call("parinfer#process_form")<CR>'
  end
end, { buffer = 0, expr = true })

vim.keymap.set("o", "k", function()
  if vim.v.operator ~= "d" then
    return "k"
  else
    return "k" .. '<Cmd>lua vim.call("parinfer#process_form")<CR>'
  end
end, { buffer = 0, expr = true })

-- p (+ indent prior to running parinfer, see :h `[)
vim.api.nvim_buf_set_keymap(
  0,
  "n",
  "p",
  'p`[v`]=<Cmd>lua vim.call("parinfer#process_form")<CR>',
  { noremap = true }
)
vim.api.nvim_buf_set_keymap(
  0,
  "n",
  "P",
  'P`[v`]=<Cmd>lua vim.call("parinfer#process_form")<CR>',
  { noremap = true }
)
```
Finally, I can use https://github.com/numToStr/Comment.nvim and its `post_hook` method to run parinfer after commenting out code.
```lua
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
