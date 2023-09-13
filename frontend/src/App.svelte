<script>
    import { colorScheme, NativeSelect, TextInput, Button, Space, Container, Center, AppShell, Anchor, Kbd, Box, Loader, Checkbox, SvelteUIProvider, Switch, Group, Text, Divider, Tooltip, Grid } from "@svelteuidev/core";
    import { getHotkeyHandler, focustrap, hotkey } from '@svelteuidev/composables';
    import { flipboard } from '@svelteuidev/motion';
    import { MagnifyingGlass, Plus } from 'radix-icons-svelte';


    let query = "";
    let weaponStatsInstance;
    let attackMode;
    let selectedAttackMode = 0;
    let value;
    let fullDamage = 0;
    let maxElement;
    let weaponElement = "";
    let isLoading = false;
    let inputElement;
    let removeOrAdd = true
    let rifleModifiers = [
        { label: 'serration', value: false , damage: 165 },
        { label: 'galvanized chamber', value: false , multishot: 80 },
        { label: 'hammer shot', value: false , crit_multi: 60, status_chance: 80 },
    ]
    

    let options = {
        method: "GET",
        headers: { "User-Agent": "Warframe Damage Calculator/2023.5.8" },
    };

    const elementalDamageTypes = [
        "heat",
        "cold",
        "electric",
        "toxin",
        "gas",
        "viral",
        "corrosive",
        "blast",
        "magnetic",
        "radiation",
        "true",
        "void",
    ];

    class weaponsStats {
        constructor() {
            this.code = "";
            this.error = "";
            this.name = "";
            this.uniqueName = "";
            this.description = "";
            this.type = "";
            this.tradable = true;
            this.category = "";
            this.productCategory = "";
            this.patchlogs = [];
            this.components = [];
            this.introduced = {
                name: "",
                url: "",
                aliases: [],
                parent: "",
                date: "",
            };
            this.estimatedVaultDate = "";
            this.url = "";
            this.mr = 0;
            this.riven_disposition = 0;
            this.polarities = [];
            this.thumbnail = "";
            this.tags = [];
            this.vaulted = true;
            this.marketCost = "";
            this.bpCost = "";
            this.attacks = [
                {
                    name: "",
                    crit_chance: 0,
                    crit_mult: 0,
                    status_chance: 0,
                    shot_type: 0,
                    shot_speed: 0,
                    duration: 0,
                    radius: 0,
                    speed: 0,
                    pellet: {
                        name: "",
                        count: 0,
                    },
                    charge_time: 0,
                    flight: 0,
                    falloff: {
                        start: 0,
                        end: 0,
                        reduction: 0,
                    },
                    damage: {
                        impact: 0,
                        puncture: 0,
                        slash: 0,
                        heat: 0,
                        cold: 0,
                        electric: 0,
                        toxin: 0,
                        gas: 0,
                        viral: 0,
                        corrosive: 0,
                        blast: 0,
                        magnetic: 0,
                        radiation: 0,
                        true: 0,
                        void: 0,
                    },
                    slide: "",
                    jump: "",
                    wall: "",
                    channeling: 0,
                    slam: {
                        damage: 0,
                        radial: {
                            damage: 0,
                            element: "impact",
                            proc: 0,
                            radius: 0,
                        },
                    },
                },
            ];
            this.masteryReq = 0;
            this.buildPrice = 0;
            this.buildTime = 0;
            this.skipBuildTimePrice = 0;
            this.buildQuantity = 0;
            this.consumeOnBuild = true;
            this.wikiaThumbnail = "";
            this.wikiaUrl = "";
            this.criticalChance = 0;
            this.criticalMultiplier = 0;
            this.disposition = 0;
            this.fireRate = 0;
            this.omegaAttenuation = 0;
            this.procChance = 0;
            this.releaseDate = 0;
            this.slot = 0;
            this.totalDamage = 0;
            this.vaultDate = "";
            this.wikiaThumbnail = "";
        }
    }

    /**
     * @param {any} query
     */
    async function getWarframeStats(query) {
        isLoading = true;

        if (!query) {
            isLoading = false 
            return;
        }
        const response = await fetch(
            `https://api.warframestat.us/weapons/${query}`,
            options
        );
        weaponStatsInstance = Object.assign(
            new weaponsStats(),
            await response.json()
        );
        console.log(weaponStatsInstance);

        isLoading = false;
    }

    function handleSelectionChange() {
        console.log(value);

        for (let i = 0; i < weaponStatsInstance.attacks.length; i++) {
            attackMode = weaponStatsInstance.attacks[i].name == value;

            console.info(weaponStatsInstance.attacks[i]);
            console.log(selectedAttackMode);
            if (attackMode !== false) {
                selectedAttackMode = i;
                console.log(selectedAttackMode);
                break;
            }
        }
    }

    function damage() {
        fullDamage =
            (weaponStatsInstance.attacks[selectedAttackMode].damage.impact ??0) +
            (weaponStatsInstance.attacks[selectedAttackMode].damage.puncture ??0) +
            (weaponStatsInstance.attacks[selectedAttackMode].damage.slash ??0) +
            (weaponStatsInstance.attacks[selectedAttackMode].damage.heat ?? 0) +
            (weaponStatsInstance.attacks[selectedAttackMode].damage.cold ?? 0) +
            (weaponStatsInstance.attacks[selectedAttackMode].damage.electric ??0) +
            (weaponStatsInstance.attacks[selectedAttackMode].damage.toxin ??0) +
            (weaponStatsInstance.attacks[selectedAttackMode].damage.gas ?? 0) +
            (weaponStatsInstance.attacks[selectedAttackMode].damage.viral ??0) +
            (weaponStatsInstance.attacks[selectedAttackMode].damage.corrosive ??0) +
            (weaponStatsInstance.attacks[selectedAttackMode].damage.blast ??0) +
            (weaponStatsInstance.attacks[selectedAttackMode].damage.magnetic ??0) +
            (weaponStatsInstance.attacks[selectedAttackMode].damage.radiation ??0) +
            (weaponStatsInstance.attacks[selectedAttackMode].damage.true ?? 0) +
            (weaponStatsInstance.attacks[selectedAttackMode].damage.void ?? 0);

        return fullDamage;
    }

    function getWeaponElement() {
        maxElement = 0;
        weaponElement = "";

        maxElement = Math.max(
            (weaponStatsInstance.attacks[selectedAttackMode].damage.heat ?? 0),
            (weaponStatsInstance.attacks[selectedAttackMode].damage.cold ?? 0),
            (weaponStatsInstance.attacks[selectedAttackMode].damage.electric ??0),
            (weaponStatsInstance.attacks[selectedAttackMode].damage.toxin ??0),
            (weaponStatsInstance.attacks[selectedAttackMode].damage.gas ?? 0),
            (weaponStatsInstance.attacks[selectedAttackMode].damage.viral ??0),
            (weaponStatsInstance.attacks[selectedAttackMode].damage.corrosive ??0),
            (weaponStatsInstance.attacks[selectedAttackMode].damage.blast ??0),
            (weaponStatsInstance.attacks[selectedAttackMode].damage.magnetic ??0),
            (weaponStatsInstance.attacks[selectedAttackMode].damage.radiation ??0),
            (weaponStatsInstance.attacks[selectedAttackMode].damage.true ?? 0),
            (weaponStatsInstance.attacks[selectedAttackMode].damage.void ?? 0),
        );

        for (let i = 0; i < elementalDamageTypes.length; i++) {
            const damageValue = weaponStatsInstance.attacks[selectedAttackMode].damage[elementalDamageTypes[i]] ?? 0;
            if (damageValue >= maxElement) {
                weaponElement = elementalDamageTypes[i];
                break;
            }
        }

        console.log(weaponElement);
        console.log(maxElement);
        
        return {element: weaponElement, damage: maxElement};
    }

    /**
     * @param {string} arg
     */
    function updateDamage(arg)  {

        for (let i = 0; i < rifleModifiers.length; i++) {
            if (rifleModifiers[i].label == arg) {

            }
        }

        removeOrAdd = !removeOrAdd
    }

</script>

<SvelteUIProvider 
    withNormalizeCSS 
    withGlobalStyles 
    themeObserver={"dark"}
>
    <AppShell>
        <div>
            <Container >
                    <Space h="xl"/>
                    <div use:focustrap>
                        <TextInput
                            placeholder="weapon name"
                            color="pink" 
                            bind:value={query}
                            type="text"
                            radius="md"
                            icon={MagnifyingGlass}
                            rightSectionWidth={70}
                            on:keypress={(e) => e.key === "Enter" && getWarframeStats(query)}
                        >
                        <svelte:fragment slot='rightSection'>
                            {#if isLoading == true}
                                <Loader size='xs' color="grape"/>
                            {:else if isLoading == false}
                                <Kbd>Enter</Kbd>
                            {/if}
                        </svelte:fragment>
                        </TextInput>
                    </div>
                    <Space h="xs"/>
                    <!-- <Button 
                        variant='gradient'
                        radius="md"
                        on:click={async () => await getWarframeStats(query)} 
                        gradient={{from: 'grape', to: 'pink', deg: 35}}
                    />
                    <Space h="xl"/> -->
            </Container>
        </div>
        {#if isLoading == true}
        <Space h="xs"/>
        <Loader color="grape"/>
        {:else if isLoading == false}
        <Container >
            {#if weaponStatsInstance}
            <Divider labelPosition='center'>
                <div slot='label'>
                    <MagnifyingGlass />
                    <span>Search results</span>
                </div>
            </Divider>
                    {#if weaponStatsInstance.error == "No Result"}
                        <Space h="xs"/>
                        <div>no result</div>
                        <Space h="xs"/>
                        <div>{weaponStatsInstance.code}</div>
                    {/if}
                    {#if weaponStatsInstance.error !== "No Result"}
                    <Space h="xs"/>
                        <NativeSelect
                            data={weaponStatsInstance.attacks.map((attack) => attack.name)}
                            color="pink"
                            bind:value
                            placeholder="{value}"
                            aria-label="Select Weapon Mode"
                            radius="md"
                            on:change={handleSelectionChange}
                        />
                        <Group position="right" direction="column">
                            <Space h="xs"/>
                            <Grid grow={true} spacing="lg">
                                <Grid.Col span={5}>
                                    <Space h="xs"/>
                                    <Anchor href={weaponStatsInstance.wikiaUrl} target="_blank" color="dimmed">
                                        {weaponStatsInstance.name} - more info
                                    </Anchor>
                                    <Space h="xs"/>
                                    <Text align='center'>
                                        attack: {weaponStatsInstance.attacks[selectedAttackMode].name}
                                    </Text>
                                </Grid.Col>
                                <Grid.Col span={5}>
                                    <Space h="xs"/>
                                    <Text align='center'>
                                        crit chance: {weaponStatsInstance.attacks[selectedAttackMode].crit_chance}%
                                        <br />
                                        <Space h="xs"/>
                                        status chance: {weaponStatsInstance.attacks[selectedAttackMode].status_chance}%
                                    </Text>
                                </Grid.Col>
                                <Grid.Col span={5}>
                                    <Space h="xs"/>
                                    <Text align='center'>
                                        crit multiplier: {weaponStatsInstance.attacks[selectedAttackMode].crit_mult}x
                                    </Text>
                                </Grid.Col>
                                <Grid.Col span={5}>
                                    <Space h="xs"/>
                                    <Text align='center'>
                                        ips: 
                                        {weaponStatsInstance.attacks[selectedAttackMode].damage.impact ?? 0} / 
                                        {weaponStatsInstance.attacks[selectedAttackMode].damage.puncture ?? 0} / 
                                        {weaponStatsInstance.attacks[selectedAttackMode].damage.slash ?? 0}
                                    </Text>
                                    <Space h="xs"/>
                                    <Text align='center'>
                                        {((weaponStatsInstance.attacks[selectedAttackMode].damage.slash ??0) / (fullDamage = damage())) * 100}% slash
                                    </Text> 
                                </Grid.Col>
                                <Grid.Col span={4}>
                                    <Space h="xs"/>
                                    <Text component='span' align='center' size='xl' weight='bold' color="pink" >
                                        {(fullDamage = damage())} damage
                                    </Text>
                                </Grid.Col>
                            </Grid>
                        </Group>
                        <Space h="xl"/>
                        <Container override={{bc: 'grape'}}>
                            <Divider labelPosition='center'>
                                <div slot='label'>
                                    <span>damage modifiers</span>
                                </div>
                            </Divider>
                            {#if weaponStatsInstance.category == "Primary"}
                                <Group>
                                    <Space h="xs"/>
                                    <Tooltip label='+165% damage' color="pink" withArrow>
                                        <Checkbox label={rifleModifiers[0].label} color="pink" bind:checked={rifleModifiers[0].value} on:change={() => updateDamage(rifleModifiers[0].label)}/>
                                    </Tooltip>
                                    <Space h="xs"/>
                                    <Tooltip label='+80% multishot (scaling)' color="pink" withArrow>
                                        <Checkbox label={rifleModifiers[1].label} color="pink" bind:checked={rifleModifiers[1].value} on:change={() => updateDamage(rifleModifiers[1].label)}/>
                                    </Tooltip>
                                    <Space h="xs"/>
                                    <Tooltip label='+60% crit multi | +80% status chance' color="pink" withArrow>
                                        <Checkbox label={rifleModifiers[2].label} color="pink" bind:checked={rifleModifiers[2].value} on:change={() => updateDamage(rifleModifiers[2].label)}/>
                                    </Tooltip>
                                </Group>  
                            {:else if weaponStatsInstance.category == "Secondary"}
                                <Group>

                                </Group>    
                            {:else if weaponStatsInstance.category == "Melee"}
                                <Group>

                                </Group>
                            {/if}
                        </Container>
                        <!-- TODO: get this shit working / make it reset properly -->
                        <!-- {#if getWeaponElement().element !== ""}
                            <div>
                                {getWeaponElement().damage} {getWeaponElement().element}
                            </div>
                        {/if} -->
                    {/if}
                {/if}
            </Container>
        {/if}
    </AppShell>
</SvelteUIProvider>

<style>
</style>
