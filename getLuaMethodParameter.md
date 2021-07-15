
Sometime just want to record information but you can't change existing code.  
Use debug.hook to solve it.  
  
  
```
function getVarsString(varsTable)
    if type(varsTable) ~= "table" then
        return "";
    end

    local ret = "";
    for k,v in pairs(varsTable) do
        if tostring(v) ~= "" then
            ret = ret.. tostring(k).. "=".. tostring(v).. ", ";
        end
    end

    return string.gsub(ret, ", $", "");
end

function hook()
    local info = debug.getinfo(2);
    if info ~= nil and info.what == "Lua" then
        local i, variables = 1, {""};
        while true do
            local name, value = debug.getlocal(2, i);
            if name == nil then
                break;
            end
            -- record used parameter
            if name ~= "(*temporary)" then
                variables[tostring(name)] = value;
            end
            i = i + 1;
        end
        -- show what you called
        print((info.name or "unknown").. "(".. getVarsString(variables).. ")");
    end
end

debug.sethook(hook, "c");

-- test code ----------------------------------------
function emptyMethod(arg1, asdf) end
emptyMethod("some string", 2012);

local a = {
    test = function(name, age)
        return 10;
    end
};
a.test("otto", 19);
-- console output -----------------------------------
--
-- emptyMethod(arg1=some string, asdf=2012)
-- test(age=19, name=otto)
--
```
