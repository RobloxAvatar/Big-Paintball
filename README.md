local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()

local Window = Library.CreateLib("Big Paintball", "Serpent")

local MainTab = Window:NewTab("Main")

local MainSection = MainTab:NewSection("Main")

oldDamage = nil
oldFireRate = nil
oldVelocity = nil
oldAutomatic = nil
oldAdditionalSpeed = nil
oldGadgetDamage = nil

for i,v in next, getgc(true) do
    if type(v) == "table" and rawget(v, "damage") then
        oldDamage = v.damage
        oldFireRate = v.firerate
        oldVelocity = v.velocity
        oldAutomatic = v.automatic
        oldAdditionalSpeed = v.additionalspeed
        oldGadgetDamage = v.gadgetDamage
    end
end

MainSection:NewToggle("Gun Mod", "toggle the gun mod!", function(state)
    if state then
        toggled = true
        if toggled == true then
            for i,v in next, getgc(true) do
                if type(v) == "table" and rawget(v, "damage") then
                    v.damage = math.huge
                    v.firerate = 0.000000001
                    v.velocity = 50000
                    v.automatic = true
                    v.additionalspeed = 10
                    v.gadgetDamage = math.huge
                end
            end
        end
    else
        toggled = false
        if toggled == false then
            for i,v in next, getgc(true) do
                if type(v) == "table" and rawget(v, "damage") then
                    v.damage = oldDamage
                    v.firerate = oldFireRate
                    v.velocity = oldVelocity
                    v.automatic = oldAutomatic
                    v.additionalspeed = oldAdditionalSpeed 
                    v.gadgetDamage = oldGadgetDamage
                end
            end
        end
    end
end)

MainSection:NewButton("Esp", "gives esp!", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/RobloxAvatar/Owl-Esp/main/Readme.lua", true))()
end)

MainSection:NewButton("Unlock All Weapons", "unlocks all weapons!", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/RobloxAvatar/Big-Paintball/main/Unlock-All.lua", true))()
end)

MainSection:NewButton("Silent Aim", "opens owl hub for silent aim", function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/CriShoux/OwlHub/master/OwlHub.txt", true))()
end)
