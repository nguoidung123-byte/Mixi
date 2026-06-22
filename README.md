local Config = {
    Scan = 2.0,
    Interact = 0.05,
    Plots = {"Plots", "Gardens", "PlayerPlots"},
    Keywords = {"harvest", "thu hoạch", "pick", "plant", "trồng", "seed"}
}

local function getFolder()
    for _, name in ipairs(Config.Plots) do
        local folder = workspace:FindFirstChild(name)
        if folder then return folder end
    end
end

local function check(prompt)
    if not prompt:IsA("ProximityPrompt") or not prompt.Enabled then return false end
    local text = string.lower((prompt.ActionText or "") .. (prompt.ObjectText or ""))
    for _, kw in ipairs(Config.Keywords) do
        if string.find(text, kw) then return true end
    end
    return false
end

if _G.Stop then _G.Stop() end
_G.Active = true
_G.Stop = function() _G.Active = false end

task.spawn(function()
    while _G.Active do
        local folder = getFolder()
        if folder then
            for _, plot in ipairs(folder:GetChildren()) do
                for _, obj in ipairs(plot:GetDescendants()) do
                    if not _G.Active then return end
                    if check(obj) then
                        fireproximityprompt(obj)
                        task.wait(Config.Interact)
                    end
                end
            end
        end
        task.wait(Config.Scan)
    end
end)
