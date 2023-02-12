 Below deomonstrates how to use LPH_JIT macros to optimize your script for obfuscation 


First add this to the top of your script so it can work when obfuscated and when not for testing
```lua
if not LPH_OBFUSCATED then
    LPH_JIT_MAX = function(...) return(...) end;
    LPH_NO_VIRTUALIZE = function(...) return(...) end;
end
```
 

example, this would run very slow when obfuscated if not in a JIT macro because it is a very heavy loop
```lua
for _, v in pairs(getgc()) do
    if type(v) == 'function' then
        print('WE HAVE A FUNCTION!')
    end
end
```

simple to optimize this, we can use either LPH_JIT_MAX or LPH_NO_VIRTUALIZE
```lua
LPH_JIT_MAX(function() -- limits the level of obfuscation to make loops faster
    for _, v in pairs(getgc()) do
        if type(v) == 'function' then
            print('WE HAVE A FUNCTION!')
        end
    end
end)() -- end of lph_jit_max macro - also calls it important you add the () at the end to call it
```

Above is great, improves speed but if you want it even faster you can use LPH_NO_VIRTUALIZE 
NOTE LPH_NO_VIRTUALIZE removes obfuscation from everything within it, allowing it to run at similar speeds when not obfuscated - use it wisely (where you dont care if someone can see that part)
```lua
LPH_NO_VIRTUALIZE(function() -- removes obfuscation to make this section MUCH faster
    for _, v in pairs(getgc()) do
        if type(v) == 'function' then
            print('WE HAVE A FUNCTION!')
        end
    end
end)() -- end of LPH_NO_VIRTUALIZE macro - also calls it important you add the () at the end to call it
```

PRO TIP, use JIT macros when using metamethod hooks
```lua
LPH_JIT_MAX(function() 
    local old; old = hookmetamethod(game, '__namecall', 
     newcclosure(function(self, ...)
         return old(self, ...);
     end))
 end)();
```

PRO TIP 2: Use these macros for other heavy loops such as workspace:GetDescendants(), getgc(), getreg(), etc..
